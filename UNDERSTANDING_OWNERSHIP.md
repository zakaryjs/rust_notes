# Understanding Ownership

Ownership is Rust's most unique feature. It allows Rust to be a memory safe language without the need for a garbage collector. Key features include borrowing, slices and memory.

## What is Ownership?

Ownership is a set of rules that determine how a Rust program manages memory. This set of rules is checked at compile time by the compiler.

### The Stack and the Heap

The stack and the heap are both parts of memory that are available to the code at run time. They are structured in different ways. 

The stack stores values in the order that it receives them in. It is commonly known as 'last in, first out'.

An example: A stack of plates - when plates are added, they are added to the top of the pile. When a plate is needed, it is taken from the top of the pile.

Adding data to the stack is known as `pushing onto the stack` and removing data is known as `popping off the stack`. 

All data stored on the stack is required to have a known, fixed size. Data that does not fit these requirements is instead stored on the heap.

The heap is less organised than the stack. When data is put on the heap, a certain amount of space is requested. The memory allocator then finds a spot that is big enough to store the data, marks it as being in use, and returns a pointer. A pointer is an address of the location for which the data is stored. The process as a whole is known as `allocating on the heap`. 

The pointer itself that points to the part of the heap where the data is stored has a known, fixed size, and as such can be stored on the stack. When the data wants to be used, the pointer must be followed to find the data on the heap.

An example: When you enter a restaurant and want to be seated, you state the number of people in your group, and the waiter finds an empty table for you. If someone from your group comes late, they can ask where you have been seated in order to find you.

Storing data on the stack is faster than allocating it on the heap as the allocator doesn't need to find a place to store that data. The data will always be added to the top of the stack. Allocating space on the heap is more intensive as it requires the allocator to find space on the heap and then store the pointer in the stack.

Furthermore, accessing data on the heap is slower than the stack as it requires a pointer to be followed. It is easier for the processor to work with data that is closer together, on the stack, rather than further away, on the heap. 

An example: It is easier for a waiter to get the orders from an entire table before moving onto the next one, rather than jumping between tables ordering from a single person from each table multiple times over.

When a function is called, the values that are passed into the function, including parameters, pointers, and other variable data, get pushed onto the stack. When the function has completed, the values are removed from the stack.

The concept of Ownership is what Rust uses in order to keep track of what is on the stack and heap, and ensuring that everything runs smoothly.

### Ownership Rules

The key ownership rules are:

- Each value in Rust has an owner

- there can only be one owner at a time

- When the owner goes out of scope, the value will be dropped.

### Variable Scope

```rust
fn main() {
    let s = "hello"; // new string literal, hardcoded

    { // new s is not valid here
        let s = "hello"; // new string literal, though only valid in this scope
    } // new s is no longer valid
}
```

When a variable comes into scope, it is valid
When a variable is no longer in scope, it is no longer valid

### The String Type

`String literals` are immutable values, that are hardcoded into a program. They are known, fixed values.

The second string type, `String`, is good for things such as user input, and other string values that will not be known at compile time, as well as values that could be edited.

Strings are created by the `from` method, such as:

```rust
let new_string = String::from("hello!");
```

The double colons `::`, indicates that we are referring to a method/function from the `String module`.

The `string` type can be mutated, using the `push_str` method, which appends a literal string.

The main difference between string literals and Strings, is that string literals are immutable, whereas Strings are mutable. This is due to the fact that they are handled differently by memory.

### Memory and Allocation

As string literals, are immutable, their contents are known at compile time. They are hard coded into the executable. Strings, however, are mutable, and as such their content and size is not always known. 

Memory that is to be used by a String is requested by the memory allocator at runtime. After the string has been used, memory is returned to the allocator.

Memory is first requested when `String::from` is called. 

Memory is then automatically returned once the variable that owns it goes outside of scope.

```rust
fn main() {

    {
        let s = String::from("Hello"); // new string literal, though only valid in this scope

        // stuff is done with s
    } // new s is no longer valid

}
```

In the above example, memory is first requested when s calls `String::from`, and is then returned once the scope ends. At the end of the scope, Rust calls a function known as `drop` in order to free the memory.

### Variables and Data Interacting with Move

Multiple variables interact with the same data in a multitude of ways. 

Integers:

```rust
let x = 5;
let y = x;
```

In this code, the integer 5 is being binded to a variable, `x`. The variable `y` is a copy of `x`. Both variables are now equal to 5. 

As integers are scalar values with a known, fixed size, these variables are stored on the stack.

Strings:

```rust
    let s1 = String::from("hello");
    let s2 = s1;
```

Though this looks similar to the previous example, in that s2 would simply be a copy of s1. But that isn't the case. 

A `String` is made up of three parts:

- A pointer to the memory that holds the contents of the string.

- A length. This is how much memory, in bytes, that the contents of the string are currently using.

- A capacity. This is the total amount of memory has received from the allocator.

![String in memory](https://doc.rust-lang.org/book/img/trpl04-01.svg)

When s2 is told to be equal, the `String` data is copied. This is not the contents of the string itself, rather the pointer, length and capacity.

![Copied string in memory](https://doc.rust-lang.org/book/img/trpl04-02.svg)

This is different to the example where an integer was copied. The difference between the two examples is that integers are simple values, that cannot change. Strings are more complex, and do not have a definite size. As such, copying an entire string could be a very expensive operation.

When a variable goes out a scope, Rust will automatically drop the variable and clean up the memory. This is an issue, though, with the string example, as when s1 and s2 go out of scope, they will both try and free the same memory, as they both share the same pointer.

Rust prevents this however, by simply considering s1 to no longer be valid after `s2 = s1` is declared. Therefore, when s2 is dropped, there are no memory safety issues.

The following example will produce an error:

```rust
fn main() {
    let s1 = String::from("hello");

    let s2 = s1;

    println!("{}, world!", s1); // borrow of moved value s1
}
```

This concept is known as a `move`.

![Move example](https://doc.rust-lang.org/book/img/trpl04-04.svg)

### Variables and Data Interacting with Clone

There are ways to create deep copies of the `String` type.

A common way of doing this is using the `clone` method.

```rust
fn main() {
    let s1 = String::from("hello");

    let s2 = s1.clone();

    println!("{}, world!", s1); // s1 is still available as a clone was created
}
```

### Stack-Only Data: Copy

As previously mentioned, Integers are stored directly on the stack, and as such are directly copied. Types that use the `copy trait` share this functionality.

Types that implement copy are:

- All integer types, e.g. `u32`

- Boolean types, `true` and `false`

- All floating-point types, e.g `f32`

- The character type, `char`

- Tuples, if they contain types that have the copy trait'

## Ownership of Functions

Passing a variable to a function will either move or copy a variable, just as assigning it to a new variable does. This example, from the Rust docs, demonstrates this:

```rust
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it's okay to still
                                    // use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```

## Return Values and Scope

Return values can also transfer ownership. This example, from the Rust docs, demonstrates this:

```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return
                                        // value into s1

    let s2 = String::from("hello");     // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  // s2 is moved into
                                        // takes_and_gives_back, which also
                                        // moves its return value into s3
} // Here, s3 goes out of scope and is dropped. s2 was moved, so nothing
  // happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {             // gives_ownership will move its
                                             // return value into the function
                                             // that calls it

    let some_string = String::from("yours"); // some_string comes into scope

    some_string                              // some_string is returned and
                                             // moves out to the calling
                                             // function
}

// This function takes a String and returns one
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope

    a_string  // a_string is returned and moves out to the calling function
}
```

Rust allows for the return of multiple values, using a tuple.