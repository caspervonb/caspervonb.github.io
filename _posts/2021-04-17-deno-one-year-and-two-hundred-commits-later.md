---
title: Building Deno, One Year and Two Hundred Patches Later
published: true
description: 
tags: javascript, typescript, webdev, deno
cover_image: https://miro.medium.com/max/1236/1*_bQTDkM7YR--X3TetTGdQg.png
---

About a year ago, I made a new year resolution to take a few months away from contracting and **finally publish a game**. I’m already thirty and some change and have been developing games for fun since I was a little brat.

That, predictably turned out pretty much like you’d expect a new year resolution to go, it never came to pass.

But the reason wasn’t that I didn’t work on it, its just that when it comes to my own projects I am really a top tier expert yak shaver so I basically ended up shaving yaks until I became an active contributor to the [Deno TypeScript and JavaScript runtime](https://deno.land) instead.

<div align="center">
  <img src="https://miro.medium.com/max/1236/1*_bQTDkM7YR--X3TetTGdQg.png" />
</div>

# From Game Development To Shaving Ten Thousand Yaks

So how on earth does Deno, a JavaScript and Typescript runtime relate to game development? it doesn’t, at-least not to someone not familiar with the ancient art of yak shaving.

For those are unfamiliar with the term, here’s Hal from Malcom in the Middle demonstrating proper technique and workflow for fixing a light bulb.

{% include youtube.html id="AbSehcT19u0" %}

Basically dipped my toe in because one of the things I was working on for the platform abstraction layer of my vaporware game was an implementation of the WebAssembly System Interface for Web Browsers.

One thing lead to another and I ended up implementing it for Deno, I actually uploaded a short video from one of the sessions.

{% include youtube.html id="H_PEvVZTKzA" %}

While working on that I ran into a lot of issues, like [read not working with stdin](https://github.com/denoland/deno/pull/6126) and [WebAssembly error propogation being broken](https://github.com/denoland/deno/pull/6137) which
were pretty much my first patches aside from some trivial renaming and such.

Found myself lacking more system call bindings for getting my WebAssembly System Interface (WASI) implementation done so I also ended up implementing a handful of system calls such as ftruncate, fstat, fsync and fdatasync which while were all fairly trivial to implement I did end up reading and
familiarising myself with large chunks of the entire codebase.

Eventually I was approached about my then userland WASI implementation and we migrated that into the standard library which is a collection of modules that lives at [https://deno.land/std](https://deno.land/std).

Having already familiarised myself with the code base and gone from knowing nothing about Rust except that we should “Rewrite it in Rust” to being fairly comfortable with it I just went to town [submitting patches after patches](https://github.com/denoland/deno/pulls?q=is%3Apr+author%3Acaspervonb)
including but not limited to the read-eval-print-loop, code coverage and test runner.

For the last couple of years I’ve been in web development land so I found it quite enjoyable getting back to my roots and be work with a systems level language again.

Who knew my love for C and and low level game development had a real world application (except for writing unfinished/unpublished games).

That’s pretty much how I got involved with Deno, it was a complete accident but a **happy** one as I really enjoy working on software that enables and helps developers build awesome things.

# Learning Rust and Dealing with Build Times

Before I dove into Deno I was consulting and contracting. Over the years I’ve done pretty much every random thing you can think of from writing Ruby on Rails applications when that was the hip thing, large scale booking systems in C# and there’s even that one time I was audited a large project’s entire npm dependency graph which I’ll never ever forget but my bread and butter for quite a few years was just writing a bunch of Node.js servers.

Writing servers is quite far from the low level C and C++ I grew up with as a young lad but I’ve always had a love for systems languages.

I saw Rust come on the scene and thought it was interesting to have a borrow checker but thought of it as somewhat of a gimmick and quickly fell into the fallacy of “Well, **I** write correct C and C++ programs I would never need that” and pretty much just brushed of Rust as hype that would go away like it has with every other C replacement language.

However, it didn't. And after actually giving it a real chance by actually writing code and contributing to Deno. The borrow checker and type system became my favourite feature overnight. The reason was quite simple, I did not really know Rust so for the first month I was effectivly just writing C and making it compile.

Deno isn’t on the scale of say V8 or Chromium in terms of size but is still a fairly large and complex codebase so having the compiler yell at me for trying to send something that did not implement send was a godsend and pretty much eliminated the need to ever ask anyone about anything.

The compiler messages are just that great; if you’ve ever seen the horrors that unfold when you forget a token in a C++ template then you can probably relate.

But, speaking of C++ template horrors there’s also a pretty major downside and that is the *the build times*…

My old workstation and laptop both died sometime during 2019 and we spent 2020 in lockdown so that wasn’t exactly a great year to be splurging on non-essential items.

That leaves me with my fairly dated Mac mini from around ~2014-2015 which obviously is a *bit underpowered for building complex systems*.

Building Deno on it takes somewhere around ~1–2 hours for a full debug build and incremental builds take anywhere from 10–30 minutes.

Productivity wise that’s fairly terrible but if there’s a will there is a way.

I forced myself to learn Vi/Vim way back in my school days and have used it pretty much ever since which turned out to actually come in handy for once.

Instead of building locally I rent a way to expensive virtual server and work remotely over a ssh connection.

It’s mostly stable with a ping low enough not to really notice and connections do drop sometimes but it brings incremental debug builds down to 2–5 minutes which is manageable.

Really looking forward to seeing what the mold project can do for us in the future here, even if it's only for development builds.

# Chasing after GitHub Sponsorships and Dealing with Burnout

Renting a server for development is however not free so I ended up applying for the [GitHub sponsors program](https://github.com/sponsors/caspervonb) in an attempt to at-least break even with the server costs sometime around July last year.

I have never been so excited in my life to receive a single dollar, despite having to wait around 60 days to receive it.

It’s really something else to have complete strangers supporting you in open source endeavours. Especially when it’s individuals, it’s incredibly humbling.

I’m really truly really grateful and all of you guys are *really freaking awesome*!

That said, it is unfortunately *not sustainable* even after a year which leads to the inevitable doom and gloom every now and then as savings have been dwindling while I’ve been spending more time on open source than doing actual
paid work over the past year. I only planned for a maximum of 6 months so we had a *mild* freakout last month when it
seemed like we were expecting a kid, not exactly great timing right now.

It’s a funny thing, on the one hand a new sponsor one month, even if its just a few bucks gets me to pull all-nighters. but at the same time I can see it’s very unlikely to that it is ever going to reach the levels I need it to be at to consider it a salary so there's always that impending doom just around the corner.

It probably doesn’t help that I really despise plugging it so I do it half-assed tongue in cheek. It would be one thing if I could hide behind a marketing account but this is just me by myself.

Every tweet I’ve ever sent out about has been followed by a cringe starting in my neck and crawling down my spine it’s such an uncomfortable thing to put out there so I tend to make a gag out of it.

# What’s Next

Bit more personal for once than my usual cold served satire but hey can’t always wear a persona on the internet.

It’s been an interesting year, I’ve learned a ton

I’m not sure whats next for me, my current level of sponsorships is definitively more than 0 but it as likely as being struck by lightning that my [GitHub sponsorships](https://github.com/sponsors/caspervonb) are gonna rocket
up to the range we need to be at in order to keep the lights on and eat on a regular basis.

I still got promises and commitments to keep to users and the community so I can't bail overnight, there’s a bunch of bug fixes and features to land for 1.10 (which is the next version of Deno).

Then some more in my backlog for 1.11 and 1.12 but further out than time will tell but I am trying to reduce the time I spend working on Deno.

By the way, I am open for new things and opportunities so do feel free to hit me up if you’re hiring and building something *awesome*.
