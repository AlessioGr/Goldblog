---
date: "2022-02-21T12:34:56.117Z"
description: "Speeding up and simplifying the transpiler configuration for a Jest project with Speedy Web Compiler (SWC)."
title: "Switching a Jest Project from Babel to SWC"
---

There's been a lot of buzz on frontend dev Twitter over the last year about the next wave of JavaScript tooling.
Tools such as [Speedy Web Compiler (SWC)](https://swc.rs) use lower-level languages such as Rust to transpile JavaScript much more quickly than older tools such as Babel.
I've been wanting to try them out for a while, so this past weekend I migrated the [tslint-to-eslint-config](https://github.com/typescript-eslint/tslint-to-eslint-config) project from [babel-jest](https://www.npmjs.com/package/babel-jest) to [@swc/jest](https://www.npmjs.com/package/@swc/jest).

> Spoilers: if you just want to see code, check the [final pull request's file changes](https://github.com/typescript-eslint/tslint-to-eslint-config/pull/1367/file).

## Existing Babel Config

tslint-to-eslint-config's existing testing configuration was pretty typical of many modern TypeScript Node apps.
It used the [babel-jest](https://www.npmjs.com/package/babel-jest) package for Jest to transpile source files.
The repository's `babel.config.js` looked like:

```js
module.exports = {
    presets: [
        ["@babel/preset-env", { targets: { node: "current" } }],
        "@babel/preset-typescript",
    ],
};
```

> I don't use [ts-jest](https://kulshekhar.github.io/ts-jest) because I've found it simpler to use Jest's Babel support instead.
> I also don't want tests to be slowed down by type checking, though we should note [ts-jest can disable type checking](https://kulshekhar.github.io/ts-jest/docs/getting-started/options/isolatedModules).

## Moving to SWC

SWC boasts significant performance improvements over Babel for being written in Rust rather than JavaScript.
[SWC's docs on usage with Jest](https://swc.rs/docs/usage/jest) indicate there are two quick steps to onboard to SWC:

1. Install `@swc/jest` as a dev dependency along with `jest`

    ```shell
    npm i -D jest @swc/core @swc/jest
    ```

2. Add a `transform` line to your `jest.config.js` file indicating to use `@swc/jest`:

    ```js
    module.exports = {
        // ...
        transform: {
            "^.+\\.(t|j)sx?$": ["@swc/jest"],
        },
    };
    ```

    (I omitted the `x?` because my project is a Node app that doesn't use `.jsx`/`.tsx` files)

Those steps are enough to direct Jest to use SWC instead of Babel to transpile files.
Some tests were already passing, but any test that included async/await needed a bit more work.

### Runtime Generators and Regenerator Runtime

My first test run saw many tests fail with `Cannot find module 'regenerator-runtime'`:

```plaintext
 FAIL  src/cli/runCli.test.ts
  ● Test suite failed to run

    Cannot find module 'regenerator-runtime' from 'src/cli/runCli.test.ts'

      1 | import { EOL } from "os";
    > 2 |
        | ^
```

[regenerator-runtime](https://www.npmjs.com/package/regenerator-runtime) is the runtime package injected by [Regenerator](https://facebook.github.io/regenerator), the source code transform used by many transpilers' transforms for generator functions.
Fun fact: async/await in JavaScript is built on generators, so if your app has any `async` or `await` keywords, the default `@swc/jest` compiler settings add a runtime `regenerator-runtime` dependency.

I found two good ways to fix the tests crashing:

-   Manually install the package as a dev dependency with `npm i -D regenerator-runtime`
-   Configure SWC to not compile away generators or async/await code

Generators and async/await have been [fully supported in Node for years](https://kangax.github.io/compat-table/es2016plus/#node8_10), including all active and maintenance LTS versions of Node.
There's really no good reason to feel a need to keep transpiling away generators for apps made to run in Node.

I changed the SWC configuration in `jest.config.js` to specify a target of ES2021:

```js
module.exports = {
    // ...
    transform: {
        "^.+\\.(t|j)s$": [
            "@swc/jest",
            {
                jsc: {
                    target: "es2021",
                },
            },
        ],
    },
};
```

...and filed [Consider defaulting jsc target to the current Node version's supported level](https://github.com/swc-project/jest/issues/74) as an issue on the [swc-project/jest](https://github.com/swc-project/jest) GitHub repo.

> **Edit 2/24/2022**: Versions 0.2.18 of `@swc/jest` will auto-detect the highest supported `jsc.target` on your system now.
> Hooray!

At this point, all my tests passed (yay!), but the code coverage calculation was slightly lower than expected. 😕

### Code Coverage Differences

SWC has an [open issue on incorrect Jest coverage](https://github.com/swc-project/jest/issues/21).
It was much worse in my project before setting `jsc.target` to `"es2021"`.
After that change there was only one file with less coverage than before: `src/index.ts`, which exclusively contains `export` statements for the package's Node API.

```ts
export { convertFileCommentsStandalone as convertFileComments } from "./api/convertFileCommentsStandalone";
export { convertTSLintConfigStandalone as convertTSLintConfig } from "./api/convertTSLintConfigStandalone";
// ...
```

`src/index.ts` had gone from 0/0 = 100% _(code coverage math is funny sometimes)_ to marking every one of its lines as uncovered.
It isn't included in any unit tests so that change felt like an improvement in reporting accuracy.

Solution: I added `"!./src/index.ts"` to the `collectCoverageFrom` array in `jest.config.js`.

At this point, all tests were passing and code coverage was the same shiny 100%-except-for-excluded-files that it was before!
Hooray! 🎉

## Performance Comparison

One of the most important rules of making decisions based on performance is to **always measure results scientifically**.
It's not enough to read a docs site that claims blazing fast speed.
You need to measure how your specific scenario matches up with and without a change, in as similar an environment as possible.

I ran the same test commands in both the Babel and SWC app builds with the following methodology:

-   Execution: `jest --clearCache` before each command. Terminal was in focus and no actions taken on open programs during test runs.
-   Hardware: Surface Laptop 4, 11th Gen Intel(R) Core(TM) i7-1185G7 @ 3.00GHz.
-   Reporting: Time ranges are rounded numbers from 7 runs with the highest and lowest values removed.
-   Software: WSL 2.0 in an Ubuntu drive via a VS Code terminal.

| Command                          | Babel Time    | SWC Time      | (old - new) / old x 100% |
| -------------------------------- | ------------- | ------------- | ------------------------ |
| `jest`                           | 18-20 seconds | 11-14 seconds | **30-40%** faster        |
| `jest --coverage --maxWorkers=2` | 23-28 seconds | 20-25 seconds | **10-13%** faster        |

> A true scientific experiment should calculate [confidence intervals](https://en.wikipedia.org/wiki/Confidence_interval) or similar data science metrics.
> I would encourage you to repeat this experiment on your own systems.

Look at that speed improvement!
It's much more pronounced in the faster dev-time version, but even a 10% improvement in test time is a success if you ask me.

One interesting note is that even with the significantly faster SWC transpiler, the standalone `jest` command still took more than 10 seconds for its remaining 60-70% time.
Much of that time is spent by Jest setting up and tearing down Node environments (which it does for each test file to keep tests isolated) and running tests.

### Dependencies Size

Runtime performance is not the only important metric for application development.
The size a package's dependencies take up is important too.
The larger and more numerous a project's dependencies, the longer it will take to install and more disk space its `node_modules` will take up.

> Yarn v2+ and other fancy package/project managers can partially mitigate size issues by sharing dependencies across packages locally but don't yet improve CI caching as much.

I'd been optimistic about the `node_modules` folder size changes from switching to SWC because the project `package-lock.json` had shrunk by about 2,000 lines.
Running `du -sh node_modules` to check the size of my package dependencies crushed my heart:

| Babel Size | SWC Size | (old - new) / old x 100% |
| ---------- | -------- | ------------------------ |
| 186M       | 330M     | **77%** larger           |

😢.

The 30-40% local test running time improvement is still worth an extra 144M for me, but that's still a little painful.

#### Digging Into Dependencies

Looking a little deeper into where SWC's size comes from:

```plaintext
du -sh node_modules/@swc/*
444K    node_modules/@swc/core
74M     node_modules/@swc/core-linux-x64-gnu
78M     node_modules/@swc/core-linux-x64-musl
24K     node_modules/@swc/jest
```

Interestingly, running `rm -rf node_modules/@swc/core-linux-x64-musl` didn't prevent subsequent tests from passing.
78M of 152.5MB -more than half!- of the `@swc/core` package install is totally unused on my system.

I filed [Both gnu and musl core distributions installed for Ubuntu Linux](https://github.com/swc-project/swc/issues/3652) as an issue on the SWC project about the duplicate architecture package.

> As of February 2022, an [npm RFC for package distributions](https://github.com/npm/rfcs/pull/519) is in review now that would allow for solving this issue.

Furthermore, a bunch of Babel package still exist in my project's `package-lock.json` because Jest packages have them as dependencies.
Running `rm -rf node_modules/*babel*` reduced `node_modules` by another 6M.
A little improvement, but still a nice one.

## In Conclusion

SWC comes with significant transpiler performance improvements over Babel.
Those runtime performance improvements come at a cost of a much larger dependency size if you're not already using SWC.
Still, the improved performance makes the Babel-to-SWC migration very much worth it for at least my Jest projects.
⚡
