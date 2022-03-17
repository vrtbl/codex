## A Quick(-sort) Example
Here's another slightly more complex example – a recursive quick-sort with questionable pivot selection:

```elm
sort = list -> match list {
    -- base case
    [] -> []

    -- pivot is head, tail is remaining
    [pivot, ..tail] -> {
        higher = filter { x -> x >= pivot } tail
        lower  = filter { x -> x <  pivot } tail

        (sorted_lower, sorted_higher) = (sort lower, sort higher)

        sorted_lower
            + [pivot]
            + sorted_higher
    }
}
```

The first thing that you should notice is the use of a `match` expression. Like ML-style languages, Passerine makes extensive use of *pattern matching* and *destructuring* as a driver of computation. A match expression takes the form:

```elm
match value {
    pattern₀ -> expression₀
    ...
    patternₙ -> expressionₙ
}
```

Each `pattern -> expression` pair is a *branch* – each `value` is against each branch in order until a branch successfully matches and evaluates – the match expression takes on the value of the matching branch. We'll take a deep dive into match statements [later](#building-a-match-expression), so keep this in mind.

You might've also noticed the use of curly braces `{ ... }` after `[head, ..tail]`. This is a *block*, a group of expressions executed one after another. Each expression in a block is separated by a newline or semicolon; the block takes on the value of its last expression.

The next thing to notice is this line:

```elm
(sorted_lower, sorted_higher) = (sort lower, sort higher)
```

This is a more complex assignment than the first one we saw. In this example, the pattern `(sorted_lower, sorted_higher)` is being matched against the expression `(sort lower, sort higher)`. This pattern is a *tuple* destructuring, if you've ever used Python or Rust, I'm sure you're familiar with it. This assignment is equivalent to:

```elm
sorted_lower  = sort lower
sorted_higher = sort higher
```

There's no real reason to use tuple destructuring here – idiomatically, just using `sort lower` and `sort higher` is the better solution. We discuss pattern matching in depth in the [next section](#pattern-matching).

Passerine also supports higher order functions (this should come as no surprise):

```elm
filter { x -> x >= pivot } tail
```

`filter` takes a predicate (a function) and an iterable (like a list), and produces a new iterable where the predicate is true for all items. Although parenthesis could be used to group the inline function definition after `filter`, it's stylistically more coherent to use blocks for *regions of computation*. What's a region of computation? A region of computation is a series of multiple expressions, or a single expression that creates new bindings, like an assignment or a function definition.

Passerine also allows lines to be split around operators to break up long expressions:

```elm
sorted_lower
    + [pivot]
    + sorted_higher
```

Although this is not a particularly long expression, splitting up lines by operations can help improve the legibility of some expressions.
