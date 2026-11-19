# Principles

1. Fight complexity first. Every dependency, layer, option and abstraction makes code harder to understand and change, so add one only when a real need pays for it. [Grug], [Ousterhout]
2. Do the simplest thing that works. Build what is needed now, skip what might be needed later and delete code that is no longer used. [KISS], [Grug]
3. Working code is not enough. Complexity builds up in small steps, so leave the design of the code you touch a little cleaner with each change. [Ousterhout]
4. Understand before you change. Find out why code exists before you remove or rewrite it. [Grug]
5. Make modules deep. A module hides a lot of functionality behind a small, simple interface. The bigger the interface, the weaker the abstraction. [Ousterhout], [Go Proverbs]
6. Pull complexity downward. Handle the hard parts inside the module so every caller gets the simple common case by default. [Ousterhout]
7. Do 1 thing well. Keep functions, modules and programs small and focused, and compose them through plain data in and plain data out. [Unix], [clean-code]
8. Prefer duplication over the wrong abstraction. Extract shared code only once the shared shape is obvious. When an abstraction needs flags and conditionals to fit a new case, inline it and start again. [Metz], [Grug], [Go Proverbs]
9. Organize by feature instead of by layer. Keep everything 1 feature needs close together, with high cohesion inside a slice and little coupling between slices. [Bogard], [Grug]
10. Design it twice and try it early. Compare at least 2 approaches before committing, get a working version running end to end soon and rebuild the clumsy parts. [Ousterhout], [Unix]
11. Explicit over implicit. Make inputs, dependencies and side effects visible at the call site. Avoid magic, hidden global state and behavior that depends on convention. [Zen]
12. Clear over clever. Code is read far more often than it is written, so pick the obvious version a reader understands in 1 pass. If it is hard to explain, it is a bad idea. [Go Proverbs], [Zen]
13. Name things precisely. A name says what the thing is or does, is searchable and means the same everywhere. A name that is hard to pick points to an unclear design. [clean-code], [Ousterhout]
14. Comment what the code cannot say. Explain why, intent and constraints that are not obvious. Skip comments that repeat the code. [Ousterhout], [clean-code]
15. Return early and keep code flat. Handle edge cases and errors first with guard clauses, so the main path stays at the lowest indentation. [Zen]
16. No boolean flags, few arguments. A boolean parameter means the function does 2 things, so split it. Pass more than 2 arguments as 1 named options object. [clean-code]
17. Parse, don't validate. Parse untrusted input once at the boundary into a precise type, for example with a [Valibot] schema, and pass that type inward so inner code never checks again. [King]
18. Make illegal states unrepresentable. Model data so invalid combinations cannot be constructed, for example a union of states instead of several optional fields. [King]
19. Errors are values. Return expected failures as typed values the caller has to handle, and keep exceptions for bugs. [Pike]
20. Define errors out of existence. Design the API so the special case is handled by the normal path, like a delete that succeeds when the item is already gone. [Ousterhout]
21. Never fail silently. When something does go wrong, fail loudly and as soon as possible. No empty catch blocks, no swallowed errors and no fallback that hides the failure. [Zen], [Unix], [Go Proverbs]

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
