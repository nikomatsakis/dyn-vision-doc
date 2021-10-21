# Have to declare auto traits up front

It's annoying that you have to write `dyn Foo + Send` and can't use auto traits. It makes sense, of course, because the whole point of `dyn Trait` is to *not* know what type is hidden, so we can't know whether it is `Send` unless we declare it. Still annoying!

