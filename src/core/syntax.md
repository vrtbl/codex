<!-- Comment from @zack466:
    While I like this introduction, it might be a little confusing for beginners.
    Beginners might not know what functions, tuples, etc are yet.
-->

## Syntax
The goal of Passerine's syntax is to make all expressions as *concise* as possible while still conserving the 'feel' of *different types* of expressions.

We'll start simple; here's a function that squares a number:

```elm
square = x -> x * x
square 4 -- is 16
```

> By the way: Passerine uses `-- comment` for single-line comments and `-{ comment }-` for nestable multi-line comments.

There are already some important things we can learn about Passerine from this short example:

Like other programming languages, Passerine uses `=` for assignment. On the left hand side is a *pattern* – in this case, just the variable `square` – which destructures an expression into a set of bindings. On the right hand side is an *expression*; in this case the expression is a *function definition*.

> Because Passerine is *expression-oriented*, the distinction between statements and expressions isn't made. In the case that an expression produces no useful value, it should return the Unit type, `()`. Assignment, for instance, returns Unit.

The function call `square 4` may look a bit alien to you; this is because Passerine uses whitespace for function calls. A function call takes the form `l e₀ ... eₙ`, where `l` is a function and `e` is an expression. `square 4` is a simple case because `square` only takes one argument, `x`... let's try writing a function that takes two arguments!

Using our definition of `square`, here's a function that returns the Euclidean distance between two points:

```elm
distance = (x1, y1) (x2, y2) -> {
    sqrt (square (x1 - x2) + square (y1 - y2))
}

origin = (0, 0)
distance origin (3, 4) -- is 5
```

Passerine is an *expression-oriented* language, because of this, it makes sense that *all functions are anonymous*. All functions take the form `p₀ ... pₙ -> e`, where `p` is a pattern and `e` is an expression. If a function takes multiple arguments, they can be written one after another; `a b c -> d` is equivalent to `a -> (b -> (c -> d))`.

The function `distance` is a bit more complex that `square`, because the two arguments are bound using *tuple destructuring*.

As you can see, `distance` takes two pairs, `(x1, y1)` and `(x2, y2)`, and *destructures* each pair into its component parts. For instance, when we call `distance` in `distance origin (3, 4)` the function pulls out the numbers that make up the pair:

- `origin`, i.e. `(0, 0)`, is matched against `(x1, y1)`, creating the bindings `x1 = 0` and `y1 = 0`.
- the tuple `(3, 4)` is matched against `(x2, y2)`, creating the bindings `x2 = 3` and `y2 = 4`.

The body of `distance`, `sqrt (...)` is then evaluated in a new scope where the variables defined about are bound. In the case of the above example:

```elm
-- call and bind
distance origin (3, 4)
distance (0, 0) (3, 4)

-- substitute and evaluate
sqrt (square (0 - 3) + square (0 - 4))
sqrt (9 + 5)
sqrt 25
5
```

Now, you may have noticed that `distance` is actually two functions. It may be more obvious it we remove some syntactic sugar rewrite it like so:

```elm
distance = (x1, y1) -> { (x2, y2) -> { ... } }
```

The first function binds the first argument, then returns a new function that binds the second argument, which evaluates to a value. This is known as *currying*, and can be really useful when writing functional code.

> To leverage currying, function calls are *left-associative*. The call `a b c d` is equivalent to `((a b) c) d`, not `a (b (c d))`. This syntax comes from functional languages like Haskell and OCaml, and makes currying (partial application) quite intuitive.

In the above example, we used `distance` to measure how far away `(3, 4)` was from the origin. Coincidentally, this is known as the *length* of a vector. Wouldn't it be nice if we could define length in terms of distance?

```elm
length = distance origin
length (5, 12) -- is 13
```

Because distance is curried, we can call it with only one of its arguments. For this reason, `length` is a function that takes a pair and returns its distance from the `origin`. In essence, we can read the above definition of `length` as:

> `length` is the `distance` from `origin`.

Transforming data through the use of and functions and pattern matching is a central paradigm of Passerine. In the following sections, we'll dive deep and show how this small core language is enough to build a powerful and flexible language.
