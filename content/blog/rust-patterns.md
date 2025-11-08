+++
title = "Rust is all about patterns"
date = 2025-11-08
+++

Everyone who is at least a bit familiar with Rust knows about the `match` statement and how it matches the given value against patterns.

Here's a simple example of it:
```rust
let n = 14;
match n {
    0 => println!("n is zero"),
    1..=9 => println!("n is a one-digit number"),
    10 | 11 | 12 => println!("n is in range [10, 12]"),
    13..=19 => println!("n ends with `teen`"),
    _ => println!("n is some other number"),
}
```
Now, writing such code is possible in any programming language just by using a couple of `if` statements. At worst it would just look less concise.

However, Rust's pattern matching is much more powerful than that. Namely, it allows us to reason about *the shape of data*, not only its value. And that is an
immensly powerful concept that permeates through the language.

And by "permeates" I really mean it.
Let's take a look at the humble `let` keyword. It's most often used when declaring variables:
```rust
let x = 42;
let name = "Ferris"
```
so you'd think that it works as follows:
```rust
let VARIABLE = EXPRESSION
```
but you'd be wrong. In reality, the syntax is:
```rust
let PATTERN = EXPRESSION
```
So even the first Rust keyword you learn is a pattern match under the hood!

Because of it, we don't have to write code like:
```rust
let p = (0.0, 2.137, 3.14);
let x = p.0;
let y = p.1;
let z = p.2;
```

Instead, we can use this snippet which does the same, but in an elegant way.
```rust
let p = (0.0, 2.137, 3.14);
let (x, y, z) = p;
```
Let's take a closer look at the last line - we're declaring variables `x`, `y` and `z` by matching `p`
(which has a form of `(f32, f32, f32)`) against the pattern `(x, y, z)`. Clearly, the first number matches the `x` "slot", the second
the `y` "slot" and the the `z` "slot" - so we get `x = 0.0`, `y = 2.137` and `z = 3.14`.

The power of pattern matching also makes the following code invalid, since there's no place for the third member
of `p` in the left-hand side pattern.
```rust
let (x, y) = p; // doesn't compile
```

To finish up, let's check out another example of pattern matching being a handy tool that saves lines of code.

Imagine a 3D rendering engine that renders a simple scene with a light source and one object.
That object will have a lot of paramaters that describe the way light rays will interact with it - a color, a specular constant, a diffuse constant and more
(the specifics don't matter here). Let's say these parameters are stored in a stuct like the one here:
```rust
struct SurfaceParams {
    color: Rgb,
    k_s: f32,
    k_d: f32,
    k_a: f32,
    // and more
}
```
Now let's pass `SurfaceParams` to a rendering function. Our first instinct is to pass a reference to the entire struct.
But then using individual parameters inside the function will become quite verbose.
```rust
fn render(surf_params: &SurfaceParams) {
    // ...
    let specular = surf_params.k_s * surf_params.color *
        angle.cos().powf(surf_params.alpha) * light_color;
    // ...
}
```
To avoid constantly writing the `surf_params` prefix, we could pass each parameter as a separate variable. But that isn't an approach that scales well.

Instead, let's use the fact that *function parameters are patterns too*.
```rust
fn render(SurfaceParams { color, k_s, k_d, k_a, /* ... */ }: &SurfaceParams) {
    // ...
    let specular = k_s * color * angle.cos().powf(alpha) * light_color;
    // ...
}
```
The number of arguments of our function hasn't exploded *and* we don't need to verbosely name the entire struct
every time we want to reference a parameter. I learned about this syntax only recently and I wish I had known it sooner, it's so nice!

As you might expect, this isn't all there is to pattern matching in Rust.
For the curious, an in-depth explanation is available [here](https://doc.rust-lang.org/book/ch19-01-all-the-places-for-patterns.html). It really is a powerful
feature of the language that often goes unnoticed.
