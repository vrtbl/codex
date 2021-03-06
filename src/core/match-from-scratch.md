## Building a `match` expression
A match expression takes a value and a number of functions, and tries to apply the value to each function until one successfully matches and runs. A match expression looks as like this:

```elm
l = Some (1, 2, 3)

match l {
    Some (m, x, b) -> m * x + b
    None           -> 0
}
```

Here's how we can match a match expression:

```elm
syntax 'match value { arms... } {
    -- TODO: implement the body
}
```

This should be read as:

> The syntax for a match expression starts with the pseudokeyword `match`, followed by the `value` to match against, followed by a block where each item in the block gets collected into the list `arms`.

What about the body? Well:

1. If no branches are matched, an error is raised.
2. If are some branches, we `try` the first branch in a new fiber and see if it matches.
3. If the function doesn't raise a match error, we found a match!
4. If the function does raise a match error, we try again with the remaining branches.

Let's start by implementing this as a function:

```elm
-- takes a value to match against
-- and a list of functions, branches
match_function = value branches -> {
    if branches.is_empty {
        error Match "No branches matched in match expression"
    }

    result = try { (head branches) value }

    if result is Result.Ok _ {
        Result.Ok v = result; v
    } else if result is Result.Error Match _ {
        match_function value (tail branches)
    } else if result is Result.Error _ {
        -- a different error occurred, so we re-raise it
        Result.Error e = result; error e
    }
}
```

I know the use of `if` to handle tasks that pattern matching excels at hurts a little, but remember, *that's why we're building a match expression!* Using base constructs to create higher-level affordances with little overhead is a core theme of Passerine development.

Here's how you could use `match_function`, by the way:

```elm
-- Note that we're passing a list of functions
description = match_function Banana ("yellow", "soft") [
    Banana ("brown", "mushy") -> "That's not my banana!",

    Banana ("yellow", flesh)
        | flesh != "soft"
        -> "I mean it's yellow, but not soft",

    Banana (peel, "soft")
        | peel != "yellow"
        -> "I mean it's soft, but not yellow",

    Banana ("yellow", "soft") -> "That's my banana!",

    Banana (color, texture)
        -> "Hmm. I've never seen a { texture } { color } banana before...",
]
```

This is already orders of magnitude better, but passing a list of functions still feels a bit... hacky. Let's use our `match` macro definition from earlier to make this more ergonomic:

```elm
syntax 'match value { arms... } {
    match_function value arms
}
```

We've added match expression to Passerine, and they already feel like language features*! Isn't that incredible? Here's the above example we used with `match_function` adapted to `match`???:

```elm
description = match Banana ("yellow", "soft") {
    Banana ("brown", "mushy") -> "That's not my banana!"

    Banana ("yellow", flesh)
        | flesh != "soft"
        -> "I mean it's yellow, but not soft"

    Banana (peel, "soft")
        | peel != "yellow"
        -> "I mean it's soft, but not yellow"

    Banana ("yellow", "soft") -> "That's my banana!"

    Banana (color, texture)
        -> "Hmm. I've never seen a { texture } { color } banana before..."
}
```

> ??? Plot twist: we just defined the `match` expression we've been using throughout this entire overview.

