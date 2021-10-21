# Not sized

The whole idea of "dyn-safe traits" was that, because `dyn Trait: Trait`, any code I write that is generic over `T: Trait` would be usable with a `dyn` trait type. But of course that's not true: I can't have `T = dyn Trait` because `T` must be `Sized`. What's more, because `dyn Trait` requires explicit indirection, the way you work with `impl Trait` is really different than how you work with `dyn Trait`. Consider something like this:

```rust
fn log_me(d: impl Debug) {
    println!("Log: {:?}", d);
}

fn main() {
    log_me(22);
    log_me('a');
    log_me(format!("Hello, world!"));
}
```

This code works great. But now perhaps you think, ah, the `Debug` trait is dyn safe, why do I need to generate a bunch of copies of `log_me`... it'd be nice to have just one. But you can't change `impl Trait` to `dyn Trait`:

```rust
fn log_me(d: dyn Debug) {
    //       ^^^^^^^^^ Error! Not Sized!
    println!("Log: {:?}", d);
}
```

This doesn't work because `dyn` types are not sized. So you have to introduce a `&` type or some other pointer:

```rust
fn log_me(d: &dyn Debug) {
    println!("Log: {:?}", d);
}

fn main() {
    log_me(22); // <-- Error! Not a `&`!
    log_me('a'); // <-- Error! Not a `&`!
    log_me(format!("Hello, world!")); // <-- Error! Not a `&`!
}
```

And now, of course, the `main` function stops compiling, because `log_me` expects references to the thing to be logged. So I have to rewrite that:

```rust
fn log_me(d: &dyn Debug) {
    println!("Log: {:?}", d);
}

fn main() {
    log_me(&22);
    log_me(&'a');
    log_me(&format!("Hello, world!"));
}
```

I hit this particularly often with closures; writing a function that takes `impl FnMut()` is much more ergonomic than taking `&mut dyn FnMut()`.

