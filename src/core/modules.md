## Modules
Passerine's module system allows large codebases to be broken out into indiviual reusable components. A module is a scopes turned into a struct, and isn't necessarily tied to the file system.

Modules are defined using the `mod` keyword, which must be followed by a block `{ ... }`. Here's a simple module that defines some math utilities:

```elm
circle = mod {
    PI     = 3.14159265358979
    area   = r -> r * r * PI
    circum = r -> r * PI * 2
}

pizza_radius = 12
slices = 8
slice_area = (circle::area pizza_radius) / slices
```

`mod` takes all top-level declarations in a block - in this case, `PI`, `area`, and `circum` - and turns them into a struct with those fields. In essence, the above is equivalent to this struct:

```elm
circle = {
    PI:     3.14159265358979
    area:   r -> r * r * PI
    circum: r -> r * PI * 2
}
```

`mod` is nice because it's an easy way to have multiple returns. In essesence, the `mod` keyword allows for first-class scoping, by turning scopes into structs:

```elm
index = numbers pos
    -> floor (len numbers * pos)

quartiles = numbers -> mod {
    sorted = (sort numbers)
    med = sorted::(index (1/2) sorted)
    q1  = sorted::(index (1/4) sorted)
    q3  = sorted::(index (3/4) sorted)
}
```

Because we used the `mod` keyword in the above example, instead of returning a single value from the function, we return a struct containing all values in the fuction:

```elm
-- calculate statistics
numbers = [1, 2, 3, 4, 5]
stats   = quartiles numbers

-- use `q1` and `q3` to calculate the interquartile range of `numbers`
iqr     = stats::q3 - stats::q1
print "the IQR of { numbers } is { iqr } "
```

This is really useful for writing functions that return multiple values.

Aside from allowing us to group sets of related values into a single namespace, modules can be defined in different files, then be imported. Here's a module defined in a different file:

```elm
-- list_util.pn
reduce = f start list -> match list {
    [] -> start,
    [head, ..tail] -> f (reduce f tail, head)
}

sum     = reduce { (a, b) -> a + b   } 0
reverse = reduce { (a, b) -> [b.., a]} []
```

This file defines a number of useful list utilities, defined in a traditional recursive style. If we want to use this module in `main.pn`, we import it using the `use` keyword:

```elm
-- main.pn
use list_util

numbers = [1, 1, 2, 3, 5]
print (list_util::sum numbers)
```

Note that the `use` keyword is essentially the same thing as wrapping the contents of the imported file with the `mod` keyword:

```elm
-- use list_util
list_util = mod { <list_util.pn> }
```

Once imported, `list_util` is just a struct. Because of this, features of the module system naturally arise from Passerine's existing semantics for manipulating structs. To import a subset of a module, we can do something like this:

```elm
reverse = { use list_util; list_util::reduce }
```

Likewise, we can import a module within a block scope to rename it:

```elm
list_stuff = { use list_util; list_util }
```

There are a number of nice properties that arise from this module system, we've just scratched the surface. As modules are just structs, the full power of passerine and its macro system are at your disposal for building extensible systems that compose well.

