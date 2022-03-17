## FAQ
**Q:** Is Passerine ready for production use?

**A:** Not yet. Passerine is still in early stages of development, with frequent breaking changes. See the [Project Roadmap](https://github.com/vrtbl/passerine/projects/1) to get an idea of what's in development.

**Q:** Is Passerine statically typed?

**A:** Currently, Passerine is strongly and dynamically¹ typed (technically structurally typed). This is partially out of necessity – Types are defined by patterns, and patterns can be where predicated. However, I've been doing a lot of research into Hindley-Milder type systems, and the various extensions that can be applied to them.

I'm working towards making a compile-time type-checker for the language, based on Hindley-Milner type inference. With this system in place, I can make some assumptions to speed up the interpreter further and perhaps monomorphize/generate LLVM IR / WASM.

This type checker is actually the target of the next release, so stay tuned!

**Q:** What about algebraic effects and kind-based macros?

**A:** I'm interested in eventually adding both these things to the language, but first I need to implement a nice type-checker and do some more research. Algebraic Effects would fill the design space of fibers, and kind based macros would provide a more solid base for passerine's macro system. Got any fresh language features you think would complement Passerine's design philosophy? Reach out!

**Q:** What is vaporization memory management?

**A:** When I was first designing Passerine, I was big into automatic compile-time memory management. Currently, there are a few ways to do this: from Rust's borrow-checker, to µ-Mitten's Proust ASAP, to Koka's Perceus, there are a lot of new and exciting ways to approach this problem.

Vaporization is an automatic memory management system that allows for *Functional but in Place* style programming. For vaporization to work, three invariants must hold:

1. All functions params are passed by value via a copy-on-write reference. This means that only the lifetimes of the returned objects need to be preserved, all others will be deleted when they go out of scope.
2. A form of SSA is performed, where the last usage of any value is not a copy of that value.
3. All closure references are immutable copies of a value. These copies may be reference-counted in an acyclical manner.

With these invariants in place, vaporization ensures two things:

1. Values are only alive where they are still *useful*.
2. Code may be written in a functional style, but all mutations occur in-place as per rule 2.

What's most interesting is that this system requires minimal interference from the compiler when used in conjunction with a VM. All the compiler has to do is annotate the last usage of the value of any variables; the rest can be done automatically and very efficiently at runtime.

Why not use this? Mainly because of rule 3: 'closure references are immutable'. Passerine is pass-by-value, but currently allows mutation in the current scope a la let-style redefinition. But this is subject to change; and once it does, it's vaporization all the way, baby!

**Q:** Aren't there already enough programming languages?

**A:** Frankly, I think we've barely *scratched* the surface of programming language design. To say that Programming Language Design is saturated and at a local maxima is to not understand the nature of software development. Passerine is largely a test as to whether I can build a modern compiler pipeline. But what I'm even more interested in is the tooling that surrounds development environments.

Case in point: text-based entry for programming languages has been around forever because it's fast. However, it's not always semantically correct. The number of correct programs is an infinity smaller than the number of possible text files. Yet it's still possible to make text-based entry systems that ensure semantic correctness while encouraging exploration. In the future, we need to develop new tools that more closely blur the line between language and environment. Pharo is a step in the right direction, as are Unison and similar efforts.

I'd like to focus more on this in the future. An interesting project would be an editor/environment like Pharo/Unison for a small minimal language, like Scheme, or perhaps even Passerine.
