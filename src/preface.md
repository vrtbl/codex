# The Passerine Codex
*An Extensive Overview of Passerine*

## Foreword
*By Isaac Clayton*

Passerine is in an early stage of development, so it's hard to pin down exactly what it will become. In this respect, we have a single point on the wall: as there are a lot of lines that can be drawn through a point, there a lot of different directions this language could take.

So in this following foreword, I would like to lay out my history with computers, and discuss what I've come to learn about them. Through this, I hope to put another metaphorical 'point on the wall': Two points form a line, of course, and I'm hoping the the line drawn by this codex and the code we have now will be enough to find the 'figure in the marble' and chisel out the rest of Passerine.

Too heavy on the metaphor? Probably.

I first thought of developing a programming language for myself when I was around 12 or 13. At the time, I had just stumbled across lisp, having learned Python a few years before (by working through Downey's *Think Python*). I was quite enthralled by the possibilities lisp opened in my mind: if programs are sets of transformations on data (*Functional Programming*), and the programs themselves are just data (*Metaprogramming*), what can't we create? Wanting to learn more, I learned Common Lisp (through *On Lisp*), and them Scheme (through the *Wizard Book*, which I'm still particularly fond of).

So, with lisp on my mind, I wrote one. It was nothing more than a toy, of course - A thin veneer of a language tacked onto Norvig's Python scheme - but it was a language nonetheless.

Lisp, of course, is more a category of languages than a single standard; the featureless landscape of parenthesis, while beautiful in all its symmetries, lacks the benefit concise notation brings. I extended the small lisp I wrote earlier into a language called *Aspen*: a small scheme with inferred parenthesis and richer syntax. This endeavor was simplistic at best - I felt I had exhausted most of what lisp had to offer for me at that time -

So I moved onto other languages. I juggled pointers with C, categorized with Haskell, tore apart datastructures with OCaml, threw exceptions with Java (then threw Java out the window), marched fractals in GLSL, beamed messages around with Erlang and Elixer, reinforced agents in Python; this is just the tip of the iceberg. Languages were the tools I used to rip through the universe of computation, exposing new corners for breaking down abstractions, if only to build them back up a bit more airtight later, perhaps. Once you begin to understand how one language maps to another — everything old being new again — minimal languages that exemplify a core aspect of computation stand out above the rest.

The brilliance of lisp stems from the fact that it is, at it's core, a LISt Processing language. Everything in lisp is built as a graph of `cons` — list cells. Because the abstract syntax tree (AST) of the language itself is nested lists of lists, and lisp is a language *built* around easily manipulating lists, it's no wonder that lisp's macro system is so powerful. Lisp is specifically built for manipulating lists, and lisp itself is lists!

One thing that I think is viewed incorrectly about lisps, though, is *homoiconicity*, or at least the usual way the word is interpreted. After all, *Homoiconicity isn’t the point*. 

For a metaprogramming system to be powerful, the language representation should match the representation that the language itself is designed to manipulate. Lisp's macro system is powerful because lisp is *designed* to operate on lists.

What matters in a metaprogramming system is selecting a language representation that the language is built to manipulate. Lisp seems to be one of the only languages to get this right. Lists, however, are *not* the best tool for modeling a wide range of structures and behaviors. 

Data should be presented in the simplest for that still preserves visual uniqueness. The semantic operators that determine how the underlying data is processed should be distinctive enough to easily be recognizable and actionable:

> "[This engages] the brain's visual processing machinery, which operates largely subconsciously, and tells the consciousness part of what it sees."
>
> — Guido van Rossum

If what matters in metaprogramming is self-representation, then requiring everything to share the same syntax — as lisp does — seems a tad counterproductive. Our brains come equipped with rich built-in visual processors: by having code and data appear to be the same, it's hard to get a feel for what the code is doing be examining the shape of it.

Abstract datatypes (ADTs) — structs and enums — are a much richer way to represent data, and ergo programming languages. As lisp is built on the axioms of list processing — `cons`, `quote`, `map`, ... — MLs (as in Standard ML & OCaml) are built on the axioms of ADTs: pattern matching, type inference, construction.

I guess I find it a little suprising, then, that lisp-style macro systems built on manipulating ADTs are uncommon in ML-style languages. It seems like the natural next step after lisp. Lisp is built for manipulating lists and has a powerful macro system built on it. ADTs are richer than lists, ML is built for manipulating ADTs — wouldn't an ML macro system built on ADTs rival lisp's expressive power?

Languages are, after all, just tools to express computation: no matter how hard we try, there will never be a language that won't make us clarify our ideas.

But we *can*, at the very least, build languages that make those ideas easier to express.

About two years ago now (as of 2020), I stumbled across Rust (I was 14 at the time). Rust opened my mind yet again to another world of possibilities. Ownership and borrowing allow us to express fine-grained control over *resources*. Being functional yet imperative, Rust also challenged my assumptions about what it meant to follow a paradigm. In the words of Saoirse:

> Pure functional programming is an ingenious trick to show you can code without mutation. Rust is an even cleverer trick to show you can just have mutation.
>
> — Saoirse (withoutboats)

Rust is now my preferred programming language. I'm not going to wax poetic and tell you that it's the best language in existence, but it's certainly a very *very* nice one. Although it may be superficially complex, it's conceptually elegant (and I mean that in the best way possible). More importantly, the community that has developed around Rust is exceptional, and I'm grateful for the interactions I've have (and have yet to have had) with the Rust community.

Now, what does any of this have to do with Passerine?

Designing a language without taking inspiration from others is a fools errand. I hope I've shown you the path I have followed to reach the point where I'm at now. Making a programming language is a bit like making a soup: take a little of everything, let it simmer for a while, and serve it while it's hot.

So: over the past few years or so, an idea for a novel programming language has been simmering in my mind. This language is the compounded framework of ideas I've developed since I've started programming, boiled down to a refined core.

This language, of course, is Passerine: a small core language that is easily extensible, with roots in Scheme and ML-flavored languages. Above all else, Passerine is the tool I've always wished I had. I find it to be useful for me, and I hope that it will be useful for you too.

— Isaac Clayton (slightknack)
