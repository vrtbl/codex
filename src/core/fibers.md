<!-- Comment from @zack466
I switched around the Fibers/Exceptions sections so fibers weren't split up.
-->

## Fibers
What do prime sieves, exceptions, and for-loops have in common? If you guessed concurrency, you won a bajillion points! Structured concurrency is a difficult problem to tackle, given how pervasive it is in the field language design.

It's important to point out that concurrency is *not* the same thing as parallelism. Concurrent systems may be parallel, but that's not always the case. Passerine subscribes to the coroutine model of structured concurrency – more succinctly, Passerine uses *fibers* – as exemplified by [Wren](https://wren.io). A fiber is a lightweight process of execution that is cooperatively scheduled with other fibers. Each fiber is like a little system unto itself that can pass messages to other fibers.

> TODO: Algebraic Effects?

### Concurrency
<!-- Fibers are for more than just isolating the context of errors. As mentioned earlier: -->

> A fiber is a lightweight process of execution that is cooperatively scheduled with other fibers. Each fiber is like a little system unto itself that can pass messages to other fibers.

<!-- Passerine leverages fibers to handle errors, but fibers are full *coroutines*. -->
To create a fiber, use the fiber keyword:

```elm
counter = fiber {
    i = 0
    loop { yield i; i = i + 1 }
}

print counter () -> prints 0
print counter () -> prints 1
print counter () -> ...
```

The `yield` keyword suspends the current fiber and returns a value to the calling fiber. `yield` can also be used to pass data *into* a fiber.

```elm
passing_yield = fiber {
    print "hello"
    result = yield "banana"
    print result
    "yes"
}

passing_yield "first"  -- prints "hello" then evaluates to "banana"
passing_yield "second" -- prints "second" then evaluates to "yes"
passing_yield "uh oh"  -- raises an error, fiber is done
```

To build more complex systems, you can build fibers with functions:

```elm
-- a function that returns a fiber
flopper = this that -> fiber {
    loop {
        yield this
        yield that
    }
}

apple_banana = flopper "Apple" "Banana"

apple_banana () -- evaluates to "Apple"
apple_banana () -- evaluates to "Banana"
apple_banana () -- evaluates to "Apple"
apple_banana () -- ... you get the point
```


Of course, the possibilities are endless.

In addition, fibers, while usually being ran in the context of another, all act as peers to each-other. If you have a reference to a fiber, it's possible to transfer to it forgetting the context in which it was called. To switch to a fiber, use `switch`.

```elm
banana_and_end = fiber {
    print "banana ending!"
}

print "the beginning..."
switch banana_and_end
print "the end."
```

`"the end."` is never displayed.

> 'Tis not the end, 'tis but the beginning... 'tis hackin' time!
