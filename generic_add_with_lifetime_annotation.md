
The source code
```rust
// src/generic_add_with_lifetime.rs
use std::time::Duration;
fn add<'a, 'b, T: Copy + std::ops::Add<Output = T>>(i: &'a T, j: &'b T) -> T {
    *i + *j
}
fn main() {
    let a: i32 = 30;
    let b: i32 = 40;
    println!("{} + {} = {}", a, b, add(&a, &b));
    let a = Duration::new(5, 0);
    let b = Duration::new(10, 0);
    println!("{:?} + {:?} = {:?}", a, b, add(&a, &b));
}


```


Cargo.toml
```ini
# ...
[[bin]]
name="generic_add_with_lifetime"
path="src/generic_add_with_lifetime.rs"
```


To run
```bash
cargo run --bin generic_add_with_lifetime
```
