---
date: "2023-01-25T01:23:45.117Z"
description: "Using the common markdown linter and disabling any rules that would intersect with dedicated formatters such as Prettier."
image: superintendent.png
title: "Configuring Markdownlint Alongside Prettier"
---

![Sketches of a surveilance camera](./superintendent.png)

<em style="display:block;margin-bottom:2rem;text-align:center;">
Concept art from the Halo 3: ODST character, Superintendent, known for saying <em>keep it clean</em>.
[<a href="https://halo.fandom.com/wiki/Superintendent?file=Concept_surveillance_cam.png " title="Source: 'Concept surveillance cam.png'">source</a>]
</em>

As a maintainer of a popular linting tool, [typescript-eslint](https://typescript-eslint.io), I often get asked how I configure linting for my projects.
My JavaScript code gets [ESLint](https://eslint.org); my TypeScript code gets [typescript-eslint](https://typescript-eslint.io) added on top, and my Markdown code gets [Markdownlint](https://github.com/DavidAnson/markdownlint).

For all three of those tools (and others like them), keep in mind that [linters are not good formatters](https://typescript-eslint.io/linting/troubleshooting/formatting#formatters-vs-linters).
Linters are great at catching bugs, enforcing stylistic decisions such as naming, and detecting violations of best practices - but they're inherently terrible at _formatting_ your code.
Whenever you enable a linter, you should make sure it isn't also trying to format your code.

## Markdownlint

I generally use [markdownlint](https://github.com/DavidAnson/markdownlint) with [markdownlint-cli](https://github.com/igorshubovych/markdownlint-cli) for linting my Markdown code.
They're fast and straightforward to configure.
You can get started with them by installing `markdownlint-cli` to your project's devDependencies:

```shell
npm i -D markdownlint-cli
```

...and then adding a script to run it on all your `.md` files:

```json
{
    "scripts": {
        "lint:md": "markdownlint \"**/*.md\" \".github/**/*.md\""
    }
}
```

## Markdownlint and Prettier

Until recently, Markdownlint didn't have a built-in way to disable all formatting lint rules the way ESLint does with [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier).
Previously, whenever I configured Markdownlint, I'd add at least the following line in my `.markdownlint.json` to make it not try to apply formatting logic:

```json
// .markdownlint.json
{
    "line-length": false
}
```

Unfortunately, depending on how you [configure Prettier](https://prettier.io/docs/en/configuration.html) (or any other auto-formatter), there are over a _dozen_ Markdownlint rules that might conflict with formatting!

```json
// .markdownlint.json
{
    "blanks-around-fences": false,
    "blanks-around-headings": false,
    "blanks-around-lists": false,
    "code-fence-style": false,
    "emphasis-style": false,
    "heading-start-left": false,
    "hr-style": false,
    "line-length": false,
    "list-indent": false,
    "list-marker-space": false,
    "no-blanks-blockquote": false,
    "no-hard-tabs": false,
    "no-missing-space-atx": false,
    "no-missing-space-closed-atx": false,
    "no-multiple-blanks": false,
    "no-multiple-space-atx": false,
    "no-multiple-space-blockquote": false,
    "no-multiple-space-closed-atx": false,
    "no-trailing-spaces": false,
    "ol-prefix": false,
    "strong-style": false,
    "ul-indent": false
}
```

## `style/prettier.json` To The Rescue

We shouldn't be expected to add any or all of those configurations to every project that uses both Markdownlint and a dedicated formatter.
That's why I [added a `style/prettier.json` extension option to Markdownlint](https://github.com/DavidAnson/markdownlint/pull/594).
Now, instead of adding all those lines to your `.markdownlint.json`, you can include a single `extends` directive:

```json
// .markdownlint.json
{
    "extends": "markdownlint/style/prettier"
}
```

Note that because that file is provided in the `markdownlint` package, you'll want to explicitly have `markdownlint` installed, even though it's already a dependency of `markdownlint-cli`:

```shell
npm i markdownlint@latest markdownlint-cli@latest -D
```

You can read more about configuring Markdownlint alongside Prettier in [Markdownlint's Prettier documentation](https://github.com/DavidAnson/markdownlint/blob/main/doc/Prettier.md).

## Aside: Sentences Per Line

By the way, I wrote my own [`sentences-per-line`](https://github.com/JoshuaKGoldberg/sentences-per-line) Markdownlint rule.
It enforces that each sentence is on its own line (or, in other words, that no line contains more than one sentence).
I like keeping to one sentence per line because it enforces simpler Git diffs and shorter, more readable lines.

```shell
npm i -D sentences-per-line
markdownlint --rules sentences-per-line
```

## Putting It All Together

You can see a repository that uses Markdownlint with formatting rules disabled over at [JoshuaKGoldberg/template-typescript-node-package](https://github.com/JoshuaKGoldberg/template-typescript-node-package).
Its [package.json `lint:md` script](https://github.com/JoshuaKGoldberg/template-typescript-node-package/blob/55da5833d893bb2f9831896dfa23bd027fc04cfb/package.json#LL60) runs Markdownlint with `sentences-per-line` on all Markdown files (including those in the `.github/` directory, since dotfile folders aren't linted by default):

```json
{
    "scripts": {
        "lint:md": "markdownlint \"**/*.md\" \".github/**/*.md\" --rules sentences-per-line"
    }
}
```

Its [`.markdownlint.json`](https://github.com/JoshuaKGoldberg/template-typescript-node-package/blob/55da5833d893bb2f9831896dfa23bd027fc04cfb/.markdownlint.json) extends from `markdownlint/style/prettier` and disables a couple more rules I tend to prefer off in my repositories:

```json
{
    "extends": "markdownlint/style/prettier",
    "first-line-h1": false,
    "no-inline-html": false
}
```

Anyway, thanks for reading.
I hope you found some of this useful!
🧹
