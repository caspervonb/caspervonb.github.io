---
title: Test Your JavaScript and TypeScript Documentation with Deno
layout: post
---

For Deno version 1.10 I introduced the first of milestone for documentation testing with type checking of your documentation blocks.

The immediate benefit of that was pretty great and quickly let me pinpoint a bunch of broken examples like these:
![Screenshot of closed pull requests containing documentation fixes for denoland/deno](https://res.cloudinary.com/practicaldev/image/fetch/s--cluzb10g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6szgsstdczpjckjxgev5.png)

I ran out of time for Deno version 1.10, final days before shipping was filled with sniffles, fever and fatigue so what we shipped in that release was a stripped down version of what I had implemented as a proof of concept but wasn't willing to ship it just yet.

# Syntax
The syntax is pretty straight forward, to make a code block executable it just needs to be a fenced code block.
Attributes are used to further specify language and special behavior of the code block:

~~~ts
/**
 * ```ts
 * console.log("Hello, world");
 * ```
 */
~~~

Sometimes, you need some setup code, or other things that would distract from your example, but are important to make the examples work.

For this reason we also support hiding lines from the rendered documentation output by starting the line with a hash sign:

~~~ts
/**
 * ```ts
 * # const a = 1;
 * # const b = 2;
 * console.log(a, b);
 * ```
 */
~~~
## Compilation
One of the more common mistakes in the wild, and also in our documentation was trivial editor oopsies and out-of-date examples.
Compiling your examples will let the type-checker catch all of those errors ahead of time.

~~~ts
/**
 * ```ts
 * import { example } from "./mod.ts";
 * 
 * console.log(exampel());
 * ```
 */
 export function example(): string {
   return "example";
}
~~~

Which will fail because we mispelled example:

~~~sh
deno test --doc mod.ts
Check file:///Users/caspervonb/mod.ts:2-7
error: TS2552 [ERROR]: Cannot find name 'exampel'. Did you mean 'example'?
console.log(exampel());
            ~~~~~~~
    at file:///Users/caspervonb/mod.ts:2-7.ts:3:13
~~~

One can opt out of type-checking with `no-check`.

~~~ts
/**
 * ```ts no-check
 * import { example } from "./mod.ts";
 * 
 * console.log(exampel());
 * ```
 */
 export function example(): string {
   return "example";
}
~~~
## Linting

I also implemented support linting documentation examples for those who like to ensure that examples are lint free:

~~~ts
/**
 * ```ts
 * import { example } from "./mod.ts";
 * 
 * function snake_case() {
 *   return example();
 * }
 * console.log(snake_case());
 * ```
 */
 export function example(): string {
   return "example";
}
~~~

Which will fail because we have the camelcase rule enabled.

~~~sh
deno lint --doc mod.ts
(camelcase) Identifier 'snake_case' is not in camel case.
function snake_case() {
         ^^^^^^^^^^
    at /Users/caspervonb/mod.ts:3:9

    hint: Consider renaming `snake_case` to `snakeCase`

Found 1 problem
Checked 1 file
~~~
## Running

Finally, examples can be run to ensure that they actually work as intended.
An example is run as a standalone process and considered successful as long as it does not throw.

~~~ts
/**
 * ```ts
 * import { success } from "./mod.ts";
 * 
 * success();
 * ```
 */
 export function success(): void {
   // no-op (e.g exit with 0).
}
~~~

Sometimes you want to demonstrate that something throws howver, so this can be inversed with the `should-throw` attribute flipping the expectation so that not throwing is the failure state.

~~~ts
/**
 * ```ts should-throw
 * import { throws } from "./mod.ts";
 * 
 * throws();
 * ```
 */
 export function throws(): void {
   throw new Error("Oops!");
}
~~~

Finally, for complex scenarios where it doesn't make sense to run the example you can just opt out of running it with the `no-run` attribute.

~~~ts
/**
 * ```ts no-run
 * import { complex } from "./mod.ts";
 * 
 * complex();
 * ```
 */
 export function complex(): void {
  // rely on external servers and complex things.
}
~~~
# More to come
This is still a work in progress, only compilation has actually shipped in a release.
But, this is all too fundemental to not ship so, it is coming.

Additionally, there are more fun things to explore here.
Being able to run things like `deno lint --fix --doc` would be awesome.
