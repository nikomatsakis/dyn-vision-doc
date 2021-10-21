# Lifetime defaults are different between dyn and impl Trait

Another challenge that happens when migrating from impl Trait to dyn Trait is with object lifetime defaults. Imagine you are taking ownership of something:

```rust
fn log_then_drop_me(x: impl Debug) { }
fn main() {
    log_then_drop_me(&22);
}
```

and you decide that you want to convert this to use `dyn`. You will need a `Box` to take ownership:

```rust
fn log_then_drop_me(x: Box<dyn Debug>) { }
fn main() {
    log_then_drop_me(Box::new(&22)); // ERROR
}
```

Needing a box is annoying enough, but there's actually a subtle difference here. `dyn` traits must *always* have a lifetime bound, and `Box<dyn Debug>` defaults to `Box<dyn Debug + 'static>`. `impl Debug`, on the other hand, doesn't have any lifetime bound at all (it converts to a generic parameter). So that means that the second version won't compile unless we write `Box<dyn Debug + '_>`, explicitly saying that we want to take a value of any lifetime.