# Principles

1. Treat complexity as the main enemy. It builds up in small steps that each look harmless, so before adding a dependency, layer, option or abstraction, check that a current need pays for it. Default to no. [Grug], [Ousterhout]
2. Do the simplest thing that solves today's problem. No speculative features, settings or generality for a future that may not come. Delete code that is no longer used. [KISS], [Grug]
3. Understand before you change. Read the callers, tests and history of code before you remove or rewrite it. Code that looks pointless often guards a case you have not seen yet. [Grug]
4. Follow the conventions already in the codebase. Use its existing names, patterns and libraries for the same job, so there is 1 obvious way to do each thing. Change a convention everywhere or nowhere. [Ousterhout], [Zen]
5. Make modules deep. Hide a lot of behavior behind a small interface. Absorb the hard parts and choose sensible defaults inside the module, so callers get the common case without configuration. A wrapper that only forwards calls hides nothing. [Ousterhout], [Go Proverbs]
6. Give each unit 1 responsibility. Functions, modules and programs do 1 thing well and compose through plain data in and plain data out. Split a unit when it does 2 unrelated things. Length alone is no reason to split. [Unix], [clean-code], [Ousterhout]
7. Prefer duplication over the wrong abstraction. A few similar lines repeated are cheaper than a shared helper with the wrong shape, so extract only once the shared shape is obvious. When an abstraction needs flags and conditionals to fit a new case, inline it back and start again. [Metz], [Grug], [Go Proverbs]
8. Organize by feature instead of by layer. Keep the handler, logic and data access of 1 feature together. A change to 1 feature should touch 1 folder, and slices should share as little as possible. [Bogard], [Grug]
9. Design it twice and try it early. For anything non-trivial, compare 2 different approaches before choosing. Then get a thin version working end to end and rebuild the clumsy parts. [Ousterhout], [Unix]
10. Test behavior through the public interface. Prefer a few tests at module boundaries over many tests that mock internals, and write a regression test with every bug fix. [Grug], [clean-code]
11. Explicit over implicit. Make inputs, dependencies and side effects visible at the call site. Avoid magic, hidden global state and action at a distance. [Zen], [Go Proverbs]
12. Clear over clever. Code is read far more often than it is written, so write the obvious version a reader gets in 1 pass. Split a dense expression into named steps. If it is hard to explain, it is a bad idea. [Go Proverbs], [Zen], [Grug]
13. Name things precisely. A name says what the thing is or does, is easy to search for and means the same everywhere. A name that is hard to pick points to an unclear design. [clean-code], [Ousterhout]
14. Comment what the code cannot say. Explain why, intent and constraints that are not obvious. If a comment only repeats the code, delete it. If it explains confusing code, simplify the code. [Ousterhout], [clean-code]
15. Return early and keep code flat. Handle edge cases and errors first with guard clauses, so the main path stays at the lowest indentation. Past 2 levels of nesting, invert a condition or extract a function. [Zen]
16. No boolean flags, few arguments. A boolean parameter means the function does 2 things, so split it into 2 functions. Pass more than 2 arguments as 1 named options object. [clean-code]
17. Parse, don't validate. Parse untrusted input once at the boundary into a precise type, for example with a [Valibot] schema. Pass that type inward so inner code never checks again. [King]
18. Make illegal states unrepresentable. Model data so invalid combinations cannot be constructed, for example a union of states instead of several optional fields or booleans. [King]
19. Errors are values. Return expected failures as typed values in the function's result, so the caller has to handle them. Keep exceptions for bugs. [Pike]
20. Define errors out of existence. Where you can, design the API so the special case is part of normal behavior, like a delete that succeeds when the item is already gone. This removes the error by design. It never means catching and ignoring one. [Ousterhout]
21. Never fail silently. When something does go wrong, fail loudly, as early as possible and with context. No empty catch blocks, no swallowed errors and no fallback or default that hides the failure. [Zen], [Unix], [Go Proverbs]

## Resources

- [John Ousterhout A Philosophy of Software Design][Ousterhout]
- [KISS principle][KISS]
- [Unix philosophy][Unix]
- [Grug Brained Developer][Grug]
- [Rob Pike Errors are values][Pike]
- [Alexis King Parse, don't validate][King] ([Valibot])
- [Jimmy Bogard Vertical Slice Architecture][Bogard]
- [clean-code]
- [Tim Peters The Zen of Python][Zen]
- [Rob Pike Go Proverbs][Go Proverbs]
- [Sandi Metz The Wrong Abstraction][Metz]

[Ousterhout]: https://web.stanford.edu/~ouster/cgi-bin/book.php
[KISS]: https://en.wikipedia.org/wiki/KISS_principle
[Unix]: https://en.wikipedia.org/wiki/Unix_philosophy
[Grug]: https://grugbrain.dev/
[Pike]: https://go.dev/blog/errors-are-values
[King]: https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/
[Valibot]: https://valibot.dev/
[Bogard]: https://www.jimmybogard.com/vertical-slice-architecture/
[clean-code]: https://github.com/labs42io/clean-code-typescript
[Zen]: https://peps.python.org/pep-0020/
[Go Proverbs]: https://go-proverbs.github.io/
[Metz]: https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction
