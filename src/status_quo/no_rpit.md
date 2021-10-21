# No impl Trait in return position


I would like to be able to take impl Trait in return, at least when the trait is dyn-safe:

```rust
trait Clone {
    fn iterate(&self) -> impl Iterator<Item = u32>;
}
```

This one's a little tricker, as I discussed in [part 4], since you want to return a `Box<dyn Iterator<Item = u32>>` -- but only when you are going through dynamic dispatch, otherwise it's not necessary.

[part 4]: https://smallcultfollowing.com/babysteps//blog/2021/10/07/dyn-async-traits-part-4/#how-could-we-have-a-trait-that-boxes-futures-but-only-when-using-dyn
