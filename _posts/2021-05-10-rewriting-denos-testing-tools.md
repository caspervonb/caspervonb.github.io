---
title: Rewriting Deno's Testing Tools
layout: post
---

For the past month or two I’ve been busy re-writing Deno’s testing tools.
We’ve had a test runner since before I got involved and it worked just fine.
But it was as barebones as you could make it so I decided to scrap it and do a rewrite.

## A new parallel runner

Our test runner was written in JavaScript, it worked but it was slow as it was a serial test runner that just merged all the tests into a single context and ran them one by one.

Meaning that if one test set a timeout for 60 seconds, or more a more realistic real world scenario: wait for a server response, nothing would until that has completed.

To deal with that, I rewrote the test runner from scratch in Rust so that I could have explicit control over the threading model.

Each test module now runs in isolation on its thread with its own scheduler and runtime.

Compilation and type-checking however is done ahead of time for the entire module graph, which makes it still fast and way faster than what we could've done in userland with multiple processes or workers.

```
deno test --jobs=4
```
	
Test times on our std test suite were cut in half from about 1 minute to 30 seconds for the entire test suite.
## Per test case permissions

Somewhat embarrasing but internally we actually haven’t  been using deno test for our own unit tests. Until now, I extended the test runner with per test case permissions letting us dog food and drop the ad-hoc test runner completely.

So your unit tests may now be defined with the permissions they require and they will be requested/revoked accordingly.

```ts
Deno.test({
  name: "write",
  permissions: {
    write: true,
  },
   async fn() {
      // ...
    },
});
```

Do note tho, this still respects the global sandboxing rules so if you run deno test with no permissions, any requests to get more permissions than what you started with will be denied.
## Improved pretty output

This is still a work in progress, but the output format is getting some love. Primarily, while rewriting the reporter in Rust I’ve added the origin of the test.

Before:
```
running 2 tests 
```

After:
```
running 2 tests from file:///home/caspervonb/test.ts
```

It’s a small thing but a lot of things had to fall into place because we couldn’t do this in JavaScript without breaking some sandboxing rules so this tiny little change has has been pending for months.

This has already landed and will ship in Deno version 1.10.

## Testable documentation code blocks

I think this is the cooler feature of the highlights. At-least I found it useful on our codebase. Turns out that a lot of our examples have that one sneaky typo in them, and are therefore broken altho the actual example itself is correct.

So what if your code examples could be type-checked and run? Well, with Deno can, at-least the type checking part, running is coming later.

Take the way to often example of a single character typo:

~~~ts
/**
 *
 * ```js
 * import { example } from "./mod.ts";
 *
 * console.log(exampel());
 * ```
 */
export function example(): string {
  return "example";
}
~~~

Because we're compiling that, the type-checker will complain:

```sh
deno test --doc mod.ts
Check file:///Users/caspervonb/mod.ts:2-7
error: TS2552 [ERROR]: Cannot find name 'exampel'. Did you mean 'example'?
console.log(exampel());
            ~~~~~~~
    at file:///Users/caspervonb/mod.ts:2-7.ts:3:13
```

I did implement running them as integration tests, in that they would pass as long as they returned 0 but time didn't permit that to be polished to it was
stripped for 1.10.

I've also playing around a little bit with enabling the same for client side code executing in the browser, but I mainly work on Deno these days so we’ll see what happens with that but it would be interesting to have pluggable runners.

## Until next time

I’ve gotta get back to writing code so this is the end of this quick little feature breakdown. Until next time, I'd actually like to hear what the community is finding lacking from Deno's test runner.
