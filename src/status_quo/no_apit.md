# No impl Trait in arg position

I would like to be able to take impl Trait in argument position and still be dyn-safe, at least when the trait is dyn-safe:

```rust
trait Sum {
    fn sum(&self, iter: impl Iterator<Item = u32>) -> u32;
}
```

For that matter, I'd like to be able to have functions like Iterator's `map` API work:


```rust
trait Iterator {
    fn map<F, U>(self, f: F) -> Map<Self, F>
    where
        FnMut(Self::Item) -> U;
}
```

We don't support either of these cases because, in principle, we have to create a monomorphized version of each of these functions for each different argument type that is given. Since we can't know the set of argument types before hand, we can't pre-populate the impl with all the functions we might need.

However, both of them are similar in that we *could* "make it work". In the case of the `Sum` trait, we could generate a copy that is specialized to `&mut dyn Iterator<Item = u32>` -- i.e., it takes a reference to a `dyn` Iterator. We could put *that* in the vtable. Now, whenever we call the `sum` method, we could make a monomorphized copy whose whole job was to create the `&mut dyn Iterator` and pass it in to the function (sort of like discussed in [part 2]). The same holds for Iterator: except for the `Sized` requirement, we could instantiate `Map` with a "dyn Iterator" for `Self` that just calls `next()` dynamically.

[part 2]: https://smallcultfollowing.com/babysteps//blog/2021/10/01/dyn-async-traits-part-2/#the-dyn-impl

