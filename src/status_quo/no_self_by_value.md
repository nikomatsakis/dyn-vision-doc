# Can't take self by value

On a related note, it's very annoying that you can't take ownership of `self` in a dyn trait. Among other things, this means that I can't take an argument of type `impl FnOnce()` using a dyn trait (we did some horrible hacks to work around this for spawning closures in 1.0 days, don't ask).
