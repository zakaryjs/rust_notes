# Rust Note Taking

## Variables and Mutability

By default, variables in Rust are immutable, meaning they cannot be modified after creation.

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6
    println("The value of x is: {x}");
}

// this will produce the error 'cannot assign twice to immutable variable `x`
```

To make a variable mutable, add `mut` in front of the variable name.

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {x}");
    x = 6
    println("The value of x is: {x}");
}

// this will produce two lines; one showing that x = 5, and one showing x = 6
```

### Constants

Constants are similar to immutable variables, in the sense that they are not allowed to change. The differences are:

- Constants are always immutable, they are not allowed to change under any circumstance.

- They are declared using `const` instead of `let`.

- The type must always be annotated.

- Constants should be written with all caps; e.g.

```rust
const VALUE: u32 = 500 + 50;
```

### Shadowing

Shadowing is the concept of declaring a variable with the same name as a previously declared variable. 

It is common to say that the first variable is `overshadowed` by the second, meaning that the compiler will see the second variable when the variable is used. 

An example:

```rust
fn main() {
    let x = 5; // x = 5

    let x = x + 1; // x now = 6

    {
        let x = x * 2;
        println!("The value of x is: {x}"); // x inside the inner scope is = 12
    }

    println!("The value of x is: {x}"); // x still = 6 in the outer scope 
}
```

Shadowing is different to editing a mutable variable; a mutable variable cannot have its type changed. A shadowed variable can, as a shadowed variable is a new variable. 

```rust
let mut spaces = "   ";
spaces = spaces.len(); // this will produce a compiler error stating that a mutablevariables type cannot change
```