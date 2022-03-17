## Pattern Matching
In the last section, we touched on pattern matching a little. I hope to now go one step further and build a strong argument as to why pattern matching in Passerine is such a powerful construct. Patterns are used in in three places:

1. Assignments,
2. Functions,
3. and Type Definitions.

We'll briefly discuss each type of pattern and the context in which they are used.

### What are patterns?
*Patterns* extract *data* from *types* by mirroring the structure of those types. The act of applying a pattern to a type is called *matching* or *destructuring* – when a pattern matches some data successfully, a number of *bindings* are produced.

Passerine supports algebraic data types, and all of these types can be matched and destructured against. Here is a table of Passerine's patterns:

> In the following table, `p` is a nested sub-pattern.

| pattern  | example           | destructures |
| -------- | ----------------- | ------------ |
| variable | `foo`             | Terminal pattern, binds an variable to a value. |
| data     | `420.69`          | Terminal pattern,  data that *must* match, raises an error otherwise. See the following section on fibers and concurrency to get an idea of how errors work in Passerine. |
| discard  | `_`               | Terminal pattern, matches any data, does not produce a binding. |
| label    | `Baz p`           | Matches a label, i.e. a named *type* in Passerine. |
| tuple    | `(p₀, ...)`       | Matches each element of a tuple, which is a group of elements, of potentially different types. Unit `()` is the empty tuple. |
| list     | `[]`/`[p₀, ..p₁]` | `[]` Matches an empty list - `p₀` matches the head of a list, `..p₁` matches the tail.
| record   | `{f₀: p₀, ...}`   | A record, i.e. a struct. This is a series of field-pattern pairs. If a field does not exist in the target record, an error is raised. |
| enum     | `{p₀; ...}`       | An enumeration, i.e. a union. Matches if any of the patterns hold. |
| is       | `p₀: p₁`          | A type annotation. Matches against `p₀` only if `p₁` holds, errors otherwise. |
| where    | `p \| e`          | A bit different from the other patterns so far. Matches `p` only if the expression `e` is true. |

That's quite a lot of information, so let's work through it. The simplest case is a standard assignment:

```elm
a = b
-- produces the binding a = b
```

This is very straightforward and we've already covered this, so let's begin by discussing matching against *data*. The following function will return the second argument if the first argument passed to the function is `true`.

```elm
true second -> second
```

If the first argument is not true, say `false`, Passerine will yell at us:

```
Fatal Traceback, most recent call last:
In src/main.pn:1:1
   |
 1 | (true second -> second) false "Bananas!"
   | ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
In src/main.pn:1:2
   |
 1 | (true second -> second) false "Bananas!"
   |  ^^^^
   |
Runtime Pattern Matching Error: The data 'false' does not match the expected data 'true'
```

*Discard* is another simple pattern – it does nothing. It's most useful when used in conjunction with other patterns:

```elm
-- to ensure an fruit has the type Banana:
banana: Banana _ = mystery_fruit

-- to ignore an item in a tuple:
(_, plays_tennis, height_in_feet) = ("Juan Milan", true, 27.5)

-- to ignore a field on a record:
{ name: "Isaac Clayton", age: _, skill } = isaac
```

A *label* is a name given to a type. Of course, names do not imply type safety, but they do do a darn good job most of the time:

```elm
-- make a soft yellow banana:
banana = Banana ("yellow", "soft")

-- check that the banana flesh is soft:
if { Banana (_, flesh) = banana; flesh == "soft" } {
    print "Delicioso!"
}
```

Pattern matching on labels is used to *extract* the raw data that is used to construct that label.

*Tuples* are fairly simple – we already covered them – so we'll cover records next. A record is a set of fields:

```elm
-- define the Person type
type Person {
    name:  String,
    age:   Natural,
    skill, -- polymorphic over skill
}

-- Make a person. It's me!
isaac = Person {
    name:  "Isaac Clayton",
    age:   16,
    skill: Wizard "High enough ¯\_(ツ)_/¯",
}
```

Here's how you can pattern match on `isaac`:

```elm
Person {
    -- aliasing field `name` as `full_name`
    name: full_name,
    -- `age` is ignored
    age: _,
    -- short for `skill: skill`
    skill,
} = isaac
```

Of course, the pattern after the field is a full pattern, and can be matched against further.

*Is* is a type annotation:

```
Banana (color, _): Banana (_, "soft") = fruit
```

In this example, `color` will be bound if `fruit` is a `Banana` whose 1nd† tuple item is `"soft"`.

> † Read as 'firnd', corresponds to the 1-indexed *second* item. Zerost, firnd, secord, thirth, fourth, fifth...

Finally, we'll address my favorite pattern, *where*. Where allows for arbitrary code check the validity of a pattern. This can go a long way. For example, let's define natural numbers in terms of integers:

```elm
type Natural n: Integer | n >= 0
```

This should be interpreted as:

> The type `Natural` is an `Integer` `n` where `n` is greater than `0`.

With this definition in place:

```elm
-- this is valid
seven = Natural 7

-- this is not
negative_six = Natural -6
```

Where clauses in patterns ensure that the underlying data of a type can never break an invariant. As you can imagine, this is more powerful than ensuring name-safety through type constructors.

> TODO: traits and impls.

Pattern matching and algebraic data types allow for quickly building up and tearing down expressive data schemas. As data (and the transformation applied to it) are the core of any program, constructs for quickly building up and tearing down complex datatypes are an incredible tool for scripting expressive applications.
