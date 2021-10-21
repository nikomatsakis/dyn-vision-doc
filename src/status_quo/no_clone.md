# No clone

I very often want to be able to clone objects, but I cannot. The `Clone` trait returns `Self` and hence is not dyn-safe:

```rust
trait Clone {
    fn clone(&self) -> Self;
}
```

This case is kind of annoying because using `Self` isn't really a big problem when it appears in the return type. You could imagine that I should be able to clone a `dyn Clone` and get another `dyn Clone`, even though I don't know the hidden type. But of course we can't *return* a `dyn Clone` since we don't know how big it is.