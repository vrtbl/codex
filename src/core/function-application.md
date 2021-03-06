## Function Application
Before we move on, here's a clever implementation of FizzBuzz in Passerine:

```elm
fizzbuzz = n -> {
    test = d s x
        -> if n % d == 0 {
            _ -> s + x ""
        } else { x }

    fizz = test 3 "Fizz"
    buzz = test 5 "Buzz"
    "{n}" . fizz (buzz (i -> i))
}

1..100 . fizzbuzz . print
```

`.` is the function application operator:

```elm
-- the composition
a . b c . d

-- is left-associative
(a . (b c)) . d

-- and equivalent to
d ((b c) a)
```
