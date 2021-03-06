## Error handling
Passerine uses a combination of *exceptions* and algebraic data types to handle errors. Errors that are expected to happen should be wrapped in a `Result` type:

```elm
validate_length = n -> {
    if length n < 5 {
        Result.Error "It's too short!"
    } else {
        Result.Ok n
    }
}
```

Some errors, however, are unexpected. There are an uncountable number of ways software can fail; to account for all external circumstances is flat-out impossible in some cases.

For this reason, in the case that something that isn't expected to fail fails, an exception is raised. For example, trying to open a file that *should* exist may throw an error if it has been lost, moved, corrupted, or otherwise has incorrect permissions.

```elm
config = Config.parse (open "config.toml")
```

> `.` is the indexing operator. On a list or tuple, `items.0` returns the zerost item; on a record, `record.field` returns the specified field; **on a label, `Label.method` returns the specified associated method**.

The reason we don't always need to handle these errors is because Passerine follows a fail-fast, fail-safe philosophy. In this regard, Passerine subscribes to the philosophy of Erlang/Elixir:

> "Keep calm and let it crash."

The good news is that crashes are local to the fiber they occur in – a single fiber crashing does *not* bring down the whole system. The idiomatic way to handle an operation that we know may fail is to try it. `try` performs the operation in a new fiber and converts any exceptions that may occur into a `Result`:

```elm
config = match try (open "config.toml") {
    Result.Ok    file  -> Config.parse file
    Result.Error error -> Config.default ()
}
```

We know that some functions may raise errors, but how can *we* signal that something exceptionally bad has happened? We use the `error` keyword!

```elm
doof = platypus -> {
    if platypus == "Perry" {
        -- crashes the current fiber
        error "What!? Perry the platypus!?"
    } else {
        -- oh, it's just a platypus...
        work_on_inator ()
    }
}

-- oh no!
inator = doof "Perry"
```

Note that the value of raised errors can be any data. This allows for programmatic recovery from errors:

```elm
-- socket must not be disconnected
send_data = (
    socket
    data
) -> match socket.connection {
    -- `Disconnected` is a labeled record
    Option.None -> error Disconnected {
        socket,
        message: "Could not send data; disconnected",
    }
    -- if the connection is open, we send the data
    Option.Some connection -> connection.write data
}
```

Let's say the above code tries to send some data through a socket. To handle a disconnection, we can try the error:

```elm
ping = socket -> try (send_data socket "ping")

socket = Socket.new "isaac@passerine.io:42069" -- whatever
socket.disconnect () -- oh no!

result = ping socket

match result {
    Result.Ok "pong" -> ()
    Result.Error Disconnected socket -> socked.connect
}
```

Why make the distinction between expected errors (`Result`) and unexpected errors (fiber crashes)? Programs only produce valid results if the environments they run in are valid. When a fiber crashes, it's signaling that something about the environment it's running in is not valid. This is very useful to *developers* during development, and very useful to *programs* in contexts where complex long-running applications may fail for any number of reasons.

Why not only use exceptions then? Because it's perfectly possible for an error to occur that is not exceptional at all. Malformed input, incorrect permissions, missing items – these are all things that can occur and do occur on a regular basis. It's always important to use the right tool for the job; prefer expected errors over unexpected errors.

