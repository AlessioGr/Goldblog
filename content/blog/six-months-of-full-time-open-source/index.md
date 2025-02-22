---
date: "2022-09-07T01:23:45.117Z"
description: "What it's like to quit your job and try to work on open source tooling full time."
image: sisyphus.jpg
title: "Six Months of Full Time Open Source"
---

Open source software —software whose source code is freely available to anybody who wants it— has been a passion of mine for years.
That's why I quit my job at the end of January 2022 and started working full time on open source software in March of 2022 — six months ago last week!
It's been an exhilarating journey so far, and I'm happy to say I'm going to keep doing it for the foreseeable future!

The bright side of open source is that I've gotten to work on projects that I find technically interesting and/or push the state of modern web development forward.
The dark side of open source, though, is that **we as an industry have not figured out how to make open source financially sustainable for the vast majority of maintainers**.

![Sisyphus rolling a boulder up a hill](./sisyphus.jpg)

<em style="display:block;margin-bottom:2rem;text-align:center;">
Sisyphus working through a queue of issues on a popular GitHub repository
<br />
</em>

## What I've Done

I've gotten a chance to work with some amazing people on fascinating projects.
Here's a quick summary of the most notable open source work I'm proud of:

-   Numerous bugfixes, documentation improvements, and features in [TypeScript ESLint](https://typescript-eslint.io)
    -   I'm particularly happy with the work I did on [automating rule docs](https://github.com/typescript-eslint/typescript-eslint/pull/5593)
-   Numerous bugfixes, documentation improvements, and features in [TypeStat](https://github.com/JoshuaKGoldberg/TypeStat)
-   [Adding a Method Expression mutator to Stryker](https://github.com/stryker-mutator/stryker-js/pull/3508)
-   Taking over maintenance of [eslint-plugin-expect-type](https://github.com/JoshuaKGoldberg/eslint-plugin-expect-type) and fixing a few bugs
-   Sending a [couple bugfixes](https://github.com/microsoft/TypeScript/pulls?q=+is%3Apr+author%3AJoshuaKGoldberg+is%3Amerged+created%3A2022-02-28..2023-01-01) in TypeScript itself
-   Releasing [learningtypescript.com](https://learningtypescript.com) with dozens of free articles and projects for TypeScript
-   Releasing [chooseyourframework.dev](https://chooseyourframework.dev), which involved a whole suite of refactors and improvements to [EightBittr](https://github.com/FullScreenShenanigans/EightBittr)
-   Open sourcing a [Refined Saved Replies](https://github.com/JoshuaKGoldberg/refined-saved-replies) Chrome extension

I also resumed [streaming on Twitch](https://twitch.tv/joshuakgoldberg) and started uploading streams to [my YouTube channel](https://www.youtube.com/channel/UC1ag6LufUK30vkwS9lWHA8Q).

[Here's a GitHub issue search for all merged pull requests I've sent March onward in 2022](https://github.com/pulls?q=is%3Apr+author%3AJoshuaKGoldberg+is%3Amerged+created%3A2022-02-28..2023-01-01+is%3Apublic).

## Funding Is Hard

Right now, having done all that work in public, I net about $600/month in open source sponsorships:

-   $200/month on [my own GitHub sponsors page](https://github.com/sponsors/JoshuaKGoldberg)
-   $400/month for my portion of [TypeScript ESLint sponsorships](https://opencollective.com/typescript-eslint)

For reference, $600/month of a full time job is roughly half the U.S. federal minimum wage ($7.25 per hour, or ~$1,200/month).
My base salary in 2021 earned me more money each work day than a month of my open source salary now.

### Side Gigs, at a Cost

I've brought my income up to tolerable levels by consulting with private companies, releasing the [_Learning TypeScript_ book](https://learningtypescript.com), and reviewing occasional pull requests on [pullrequest.com](https://pullrequest.com).
But, those side gigs take my time and energy away from open source work.
It's a mild vicious cycle: I don't earn a sustainable amount as an open source maintainer, so I have to work on side gigs, which takes time away from my open source work, which makes it harder for me to promote myself and land sponsorships.

Fortunately, I expect the sponsorships to grow as I make more of a name for myself and my projects.
My goal is to reach $1000/month by the end of 2022.

## Lessons Learned

I've learned a heck of a lot on this open source adventure, and have a heck of a lot more to go.
These are the three biggest areas of learning I've been thinking on.

### Balancing Passion and Priority

There are a lot of fun open source projects available I could work on, ranging from cute toys to high impact developer tooling.
At first I resisted the urge to hop around.
I tried to force myself to work on what I thought was highest impact and visibility.
But, that's not how my mind works.
I'm most productive when I'm working on my daily flight of fancy.

These days I keep a list of projects I'm interested in and flit between them as I get motivated.
I'll only force myself to stay focused on a project if there's something time sensitive, such a critical bug.

> Lesson: I'm most productive when I'm following my passion.

### Promotion, Promotion, Promotion

I don't like bragging or asking for anything.
It feels wrong to spend precious time begging for money on the internet _(like this post!)_.
My instincts tell me that if I'm doing good and important work then people should compensate me appropriately.

But, that's not how the world works.
Nobody will know about me unless I actively promote myself.
And when others know who I am and what I work on, they now have a person who can learn about their ideas and/or address their pain points.
This promotion can create ripple effects since word of mouth also spreads as more and more people know about me.

Reading [_Influence is your Superpower_](https://www.google.com/books/edition/_/BGwsEAAAQBAJ) by Zoe Chance helped get me comfortable making waves.
It's a great read for anybody struggling with asking for help and/or growing their voice.

> Lesson: I shouldn't be afraid to promote myself.

### Treat Yourself

Being paid by sponsors is much more direct than working for a corporation.
I feel emotionally linked to people's sponsorships.
For a while I was reluctant to grab nice coffees, go on dinner dates, or otherwise spend on myself.
My sponsors are giving me money to work on open source, who am I to blow it on luxuries?

But, that mindset is neither healthy nor fair to myself.
I shouldn't be expected to give up on fun expenses because I've switched to sponsorships.
And I can't produce my best work if I'm restricting myself to the bare essentials.

I'm not going to go out of my way to blow money on unnecessary shenanigans, but I won't deprive myself either.

> Lesson: it's ok to treat yourself sometimes.

## What's Coming Next

If you thought my current list of projects was fun, just wait until you see what's coming up in the next six months!
My goals for Spring 2023 are:

-   Collaboratively get TypeScript ESLint to a new [6.x major version](https://github.com/typescript-eslint/typescript-eslint/milestone/8)
-   Learn the internals of TypeScript ESLintx and start meaningfully progressing on at-scale performance issues
-   Bring TypeStat up to the point where it's stable and working on common React projects
-   Standardize tooling and tackle immediately useful bugfixes and features across my smaller repos: [emojisplosion](https://github.com/JoshuaKGoldberg/emojisplosion), [konamomijisplosion](https://github.com/JoshuaKGoldberg/konamimojisplosion), [console-fail-test](https://github.com/JoshuaKGoldberg/console-fail-test), [eslint-plugin-expect-type](https://github.com/JoshuaKGoldberg/eslint-plugin-expect-type), etc.
-   Continue adding to [learningtypescript.com/articles](https://www.learningtypescript.com/articles) for the most frequently asked TypeScript questions

...and who knows what else?!
One of the magical parts of open source web development is how ever-changing the landscape is.
I look forward to learning what innovations will be state-of-the-art even months from now.

## How You Can Support Me

All those great goals don't come cheap.
I'd greatly appreciate any support you can give me to make full time open source work sustainable.

-   👉 [github.com/sponsors/JoshuaKGoldberg](https://github.com/sponsors/JoshuaKGoldberg) 👈: the most direct way to help get more open source work done is with a monthly sponsorship on GitHub Sponsors.
    -   If want one direct way to support me, this is the best one!
-   [opencollective.com/typescript-eslint](https://opencollective.com/typescript-eslint): if you want to support the general [TypeScript ESLint](https://typescript-eslint.io) maintenance team, that would be great too.
-   [learningtypescript.com](https://learningtypescript.com) contains links to buy my _Learning TypeScript_ book. A five star review is surprisingly helpful in promoting it.
-   [twitch.tv/JoshuaKGoldberg](https://twitch.tv/JoshuaKGoldberg) and [twitter.com/JoshuaKGoldberg](https://twitter.com/JoshuaKGoldberg): following/liking/subscribing helps increase my reach.
-   If you run a conference, meetup or podcast, reach out to me: I'd probably love to join!

If you or your coworkers write TypeScript code, help me help you!
The more support I get to work on TS means the less you have to do to get TS working efficiently for your own work.
Let me know over [email](mailto:blog@joshuakgoldberg.com "my email") or [@JoshuaKGoldberg on Twitter](https://twitter.com/JoshuaKGoldberg) if there are any projects in particular that you're interested in.

Thanks for reading - and happy open sourcing, everyone! 💖

---

Many thanks to Kenny Lin for invaluable proofreading & suggestions in authoring this article!
