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
spaces = spaces.len(); // this will produce a compiler error stating that a mutable variables type cannot change
```

## Data Types

Rust is a statically typed language, meaning all variables must have an assigned type at compile time. The compiler can infer types based on the value of a variable.

To add a type to variable, add `: <type>` after the variable name.

If a variable that has a type that cannot be inferred, and does not have a type declared, the compiler will show the following error:

`type annotations needed`

### Scalar Types

Scalar types represent a single value. There are four primary scalar types. These include integers. floating-point numbers, Booleans and characters. 

#### Integer Types

An integer is a whole number. 

| Length  | Signed | Unsigned |
|---------|--------|----------|
| 8-bit   | i8     | u8       |
| 16-bit  | i16    | u16      |
| 32-bit  | i32    | u32      |
| 64-bit  | i64    | u64      |
| 128-bit | i128   | u128     |
| arch    | isize  | usize    |

Each type of integer can either be signed or unsigned and has an explicit size.

Signed and unsigned refer to whether or not the number can be negative. 

If the number can be either positive or negative, then the variable should be a signed integer. If the number can only be positive, it can be represented without a sign and therefore can be stored as an unsigned integer.

### Floating-Point Types

There are two floating-point types in Rust; ```f32``` and ```f64```. These represent the numbers bit sizes. f64 is more commonly used as it is a more precise version of f32. Performance is not an issue as most modern CPUs run both types at similar speeds.

```rust
fn main() {
    let x = 7.1; // f64

    let y: f32 = 12.0; // f32
}
```

f32 is single-precision and f64 is double-precision.

### Numeric Operations

Rust supports all basic math operations including addition, subtraction, multiplication, division and remainder (%).

```rust
fn main() {
    // addition
    let sum = 12 + 10; // = 22

    // subtraction
    let difference = 100.2 - 40.1; // = 60.1

    // multiplication
    let product = 5 * 20; // = 100

    // division
    let result = 60.1 / 21.4;// = 2.808
    let int_division = 5 / 2; // = 2

    // remainder
    let remainder = 45 % 2; // = 1
}
```

### The Boolean Type

The boolean type has two possible values; true and false. They are one byte in size.

```rust
fn main() {
    let true_var = true; // implicitly bool

    let false_var: bool = false; // explicitly defined bool
}
```

### The Character Type

The character type is a primitive alphabetic type.

Variables that use the char type are defined with single quotes, instead of the double quotes that are used to define strings.

```rust
fn main() {
    let character = 'a'; // implicit char

    let explicit_char: char = 'A'; // explicitly defined char
}
```

## Compound Types

Compound types are types that can group multiple values into one type. These include tuples and arrays.

### The Tuple Type

Tuples are a type that allow for grouping together a number of values with different types into one compound type. Once they are declared, they cannot change in size.

Tuples are created by listing the types, and then values of said index inside brackets.

```rust
fn main() {
    let tuple: (i32, f64, u32) = (442, 98.43, 16);
}
```

Tuples can be destructured.

```rust
fn main() {
    let tuple: (i32, f64, u32) = (442, 98.43, 16);

    let (a, b, c) = tuple;
}
```

Tuples can also have their values accessed by using a `.` and then typing the index that is wanted to be accessed.

```rust
fn main() {
    let tuple: (i32, f64, u32) = (442, 98.43, 16);

    let 16 = x.2;
}
```

A tuple with no values is known as a unit, and is written as `()`.

### The Array Type

Arrays are another way to group together multiple values into one type. The difference is, that the values of an array must all have the same type. 

One array is one chunk of memory, that has a fixed size. It is allocated on the stack.

Arrays have a fixed length.

```rust
fn main() {
    let array = [1, 2, 3, 4, 5]
}
```

Arrays have their data allocated on the stack instead of the heap. (research this more)

Arrays are not as flexible as vectors, that can change in size.

Arrays are useful if you know that the size will not change.

```rust
let nums_1_10: [i32, 10] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

Arrays can be created to contain the same value, a certain number of times with the following syntax:

```rust
let 4 = [4; 4] // will create an array with four fours
```

Array values can be accessed similar to JavaScript, using the variable name followed by the index in square brackets.

If a user tries to access a value that does not exist inside an array, the program will exit with the following error message:

```index out of bounds: the len is (len) but the index is (index)```