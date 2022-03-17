## Macros
Passerine has a rich hygienic* syntactic macro system that extends the language itself.

*Syntactic macros*, quite simply, are bits of code that *hygienically* produce more code when invoked at compile time. Macros use a small, simple-yet-powerful set of rules to transform code.

> \* Having read Doug Hoyte's exellent [Let Over Lambda](https://letoverlambda.com/), I understand the raw power of a rich *unhygenic* macro system. However, such systems are hard to comprehend, and harder to master. Passerine aims to be as simple and powerful as possible without losing *transparency*: hygienic macro systems are much more transparent then their opaque unhygenic counterparts.

### Hygiene
Extensions are defined with the `syntax` keyword, followed by some *argument patterns*, followed by the code that the captured arguments will be spliced into. Here's a simple example: we're using a macro to define `swap` operator:

```elm
syntax a 'swap b {
    tmp = a
    a = b
    b = tmp
}

x = 7
y = 3
x swap y
```

Note that the above code is completely hygienic. the expanded macro looks something like this:

```elm
_tmp = x
x = y
x = _tmp
```

Because `tmp` was not passed as a macro pattern parameter, all uses of `tmp` in the macro body are unique unrepresentable variables that do not collide with any other variables currently bound in scope. Essentially:

```elm
tmp = 1
x = 2
y = 3
x swap y
```

Will not affect the value of `tmp`; `tmp` will still be `1`.

#### Argument Patterns
So, what is an argument pattern (an *arg-pat*)? Arg-pats are what go between:

```elm
syntax ... { }
```

Each item between `syntax` and the macro body is an arg-pat. Arg-pats can be:

- *Syntactic variables*, like `foo` and `bar`.
- Literal *syntactic identifiers*, which are prefixed with a quote (`'`), like `'let`.
- Nested argument patterns, followed by optional *modifiers*.

Let's start with *syntactic identifiers*. Identifiers are literal names that must be present for the pattern to match. Each syntactic extension is required to have at least one. For example, here's a macro that matches a *for loop*:

```elm
syntax 'for binding 'in values do { ... }
```

In this case, `'for` and `'in` are syntactic identifiers. This definition could be used as follows:

```elm
for a in [1, 2, 3] {
    print a
}
```

*Syntactic variables* are the other identifiers in the pattern that are bound to actual values. In the above example, `a` → `binding`, `[1, 2, 3]` → `values`, and `{ print a }` → `do`.

Macros can also be used to define operators†:

```elm
syntax sequence 'contains value {
    c = seq -> match seq {
        [] -> False
        [head, ..] | head == value -> True
        [_, ..tail] -> c tail
    }

    c sequence
}
```

This defines a `contains` operator that could be used as follows:

```elm
print {
    if [1, 2, 3] contains 2 {
        "It contains 2"
    } else {
        "It does not contain 2"
    }
}
```

Evidently, `It contains 2` would be printed.

> † Custom operators defined in this manner will always have the lowest precedence, and must be explicitly grouped when ambiguous. For this reason, Passerine already has a number of built-in operators (with proper precedence) which can be overloaded. It's important to note that macros serve to introduce new constructs that just *happen* to be composable – syntactic macros can be used to make custom operators, but they can be used for *so much more*. I think this is a fair trade-off to make.

*Modifiers* are postfix symbols that allow for flexibility within argument patterns. Here are some modifiers:

- Zero or more (`...`)
- Optional (`?`)

Additionally, parenthesis are used for grouping, and `{ ... }` are used to match expressions within blocks. Let's construct some syntactic arguments that match an `if else` statement, like this one:

```elm
if x == 0 {
    print "Zero"
} else if x % 2 == 0 {
    print "Even"
} else {
    print "Not even or zero"
}
```

The arg-pat must match a beginning `if`:

```elm
syntax 'if { ... }
```

Then, a condition:

```elm
syntax 'if condition { ... }
```

Then, the first block:

```elm
syntax 'if condition then { ... }
```

Next, we'll need a number of `else if <condition>` statements:

```elm
syntax 'if condition then ('else 'if others do)... { ... }
```

Followed by a required closing else (Passerine is expression oriented and type-checked, so a closing `else` ensures that an `if` expression always returns a value.):

```elm
syntax 'if condition then ('else 'if others do)... 'else finally { ... }
```

Of course, if statements are already baked into the language – let's build something else – a `match` expression.

