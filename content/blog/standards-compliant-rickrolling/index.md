---
title: "Standards Compliant Rickrolling"
date: "2022-04-01T11:11:11.117Z"
description: "How to create an effective rickroll site with the Vimeo Player SDK despite around modern web browser autoplay restrictions."
image: so-long.gif
---

> This blog post does not contain any surprise rickrolls.
> It's a safe space.
> I promise. 🧡

[Rickrolling](https://en.wikipedia.org/wiki/Rickrolling) is one of my favorite things to do online.
My personal rickroll page at [hi.joshuakgoldberg.com](https://hi.joshuakgoldberg.com "my personal rickroll page at hi.joshuakgoldberg.com") is my go-to destination for redirecting unsuspecting internet people to the prank.
I link it on my [Twitter bio](https://hi.joshuakgoldberg.com), include QR codes to it in all my conference talks, and send links to it to spam recruiters on LinkedIn.

Making a webpage that can effectively rickroll any arbitrary visitor was a surprisingly tricky task.
Modern web browsers restrict autoplaying audio and video, complicating the art of the sudden rickroll surprise.
This blog post details how and why my site works the way it does in celebration of April Fools' Day 2022.

<div style="display:block;margin-bottom:2rem;text-align:center;">
<img alt="Rick Astley Dancing'" src="/images/rick-astley-dancing.gif" />
</div>

> You can peek the source code on [github.com/JoshuaKGoldberg/standards-compliant-rickroll](https://github.com/JoshuaKGoldberg/standards-compliant-rickroll).

## A Brief History of Autoplay Irritation

Back in the day, you used to be able to plop any autoplaying `<audio>` or `<video>` on a webpage and all users visiting the site would experience them immediately.
I had used that as a form of aggressive user engagement encouragement on the [FullScreenMario](https://wikipedia.org/wiki/FullScreenMario) site to blast visitors with the old nostalgia-inducing Super Mario Bros. theme.

Unfortunately, sites being able to blast you with whatever multimedia spew they wanted made for a very user-hostile web.
Imagine all the ads you receive now on a typical news site (if you don't enable AdBlocker), but with audio mysteriously playing.
Terrifying.

Desktop web browsers started restricting media autoplay around 2017.
Reading straight from the [Chrome Autoplay announcement post](https://developer.chrome.com/blog/autoplay):

> Chrome's autoplay policies are simple:
>
> -   Muted autoplay is always allowed.
> -   Autoplay with sound is allowed if:
>     -   The user has interacted with the domain (click, tap, etc.).
>     -   On desktop, the user's [Media Engagement Index](https://developer.chrome.com/blog/autoplay/#media-engagement-index) threshold has been crossed, meaning the user has previously played video with sound.
>     -   The user has [added the site to their home screen](https://web.dev/customize-install) on mobile or [installed the PWA](https://web.dev/progressive-web-apps) on desktop.
>     -   Top frames can [delegate autoplay permission](https://developer.chrome.com/blog/autoplay/#iframe-delegation) to their iframes to allow autoplay with sound.

In other words, unless you've either interacted with a site or previously given it elevated permissions, a site is not allowed to autoplay audio.

## Crafting a Standards Complaint Rickroll

Web browser autoplay restrictions rendered ineffective my standard practice of sending the [YouTube rickroll link](https://www.youtube.com/watch?v=dQw4w9WgXcQ) _(which, I might add, recently passed **1 billion views!**)_.
I needed a site that could somehow surprise users with the sights and sounds of _Never Gonna Give You Up_.

Note that per the Chrome autoplay policy, videos are allowed to play sound as soon as the user clicks them.
That lends itself well to the following strategy for behavior on page load:

1. Display an _Enter_ button in an overlay over a rick roll video set to autoplay with sound
2. If the user clicks the button, play the video with sound
3. Whenever the video starts playing, remove the overlay

The key here is that the user thinks clicking the _Enter_ button will direct them to visit the site, when in fact it acts as a _Play_ button for the rickroll video.

### Video Hosting

Although I could get a rickroll on the site using a `<video>` tag and a `.mp4` illegally hosted on my own website, I didn't want to risk getting shut down by a DCMA takedown notice.
I really hate getting those.

Instead, I turned to the best free video embedding+hosting site on the internet: [Vimeo](https://vimeo.com).
[Vimeo `<iframe>` embedding](https://developer.vimeo.com/player/sdk/embed) allows for customizing autoplay (yes) and looping (heck yes).

```html
<iframe
    allow="autoplay; fullscreen"
    frameborder="0"
    tabindex="-1"
    src="https://player.vimeo.com/video/148751763?autoplay=1&loop=1&title=0&byline=0&portrait=0"
    style="top: 0%; left: 0%; width: 100%; height: 100%"
>
</iframe>
```

The [Vimeo Player SDK](https://developer.vimeo.com/player/sdk) provides a [`play()` method](https://developer.vimeo.com/player/sdk/reference#play-a-video) that can start playing a video.
I added the SDK to the page and used it to immediately ask to play the video.
Since it rejects its Promise if autoplay isn't allowed, I added a `.catch()` to avoid annoying red text in the browser dev tools console.

```html
<script src="https://player.vimeo.com/api/player.js"></script>
<script>
    // Finds the <iframe> and instantiates the Vimeo Player SDK on it
    const iframe = document.querySelector("iframe");
    const player = new Vimeo.Player(iframe);

    // Immediately attempt to start playing the video
    player.play().catch(() => {
        console.log("I'll get you next time...");
    });
</script>
```

### Playing On Click

I added the following code at the end of the `<script>` tag from before.
It hooks up a click event listener on the button to play the video through the Vimeo Player SDK.

```js
// Finds the <button> and hooks up its click to play the video
const button = document.querySelector("button");
button.addEventListener("click", () => {
    player.play();
});
```

I'm [told](https://twitter.com/KipEnyan/status/1509913672667340806 "Tweet from KipEnyan") iOS still might not play the video.
I'm out of ideas.
If you know how to fix it, please let me know!

### Detecting Video Play

No built-in JavaScript API or Vimeo SDK method exists to check whether the browser considers the page to have a high Media Engagement Index or equivalent.
However, the Vimeo Player SDK does include an [event listener API](https://developer.vimeo.com/player/sdk/reference#add-an-event-listener-to-a-player) able to call a function on video play start.

I added the following code to swap out the overlay on video play.

```js
// When the Vimeo Player is able to play the video...
player.on("play", () => {
    // Shows the iframe and make it tabbable
    iframe.style.display = "block";
    iframe.tabIndex = "";

    // Hides the overlay
    const overlay = document.getElementById("overlay");
    overlay.style.display = "none";
});
```

## The Final Product

You can visit the rickroll on [hi.joshuakgoldberg.com](https://hi.joshuakgoldberg.com "final rickroll page at hi.joshuakgoldberg.com").
The source code is available on [github.com/JoshuaKGoldberg/standards-compliant-rickroll](https://github.com/JoshuaKGoldberg/standards-compliant-rickroll).

If you're on Chrome, visit `about://media-engagement` and find `hi.joshuakgoldberg.com` after 1-2 visits to the site.
See how many 6+ second plays it takes your browser to trust the page to autoplay with sound.

### A Word of Caution

Harmless internet pranks are fun and help make the internet a joyous, welcoming place.
But, mean-spirited mischief and poorly implemented pranks alike make it a worse place instead.
Please don't put things online that actively make it harder for people to use the internet.

Whatever you do, I hope you use the strategies discussed in this article for good!
And hopefully some rickrolling. 😉
