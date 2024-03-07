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

## References and Borrowing

A reference is like a pointer, in the sense that it is an address that can be followed to access the data stored at that address. References are guaranteed to point to a valid value of a particular type.

The following `calculate_length` function uses a reference to an object as a parameter instead of taking ownership of the value.

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

The ampersands, or `&` symbols represent references. These allow for the functions to refer to the value without actually taking ownership of it.

![Image representation of references](https://doc.rust-lang.org/book/img/trpl04-05.svg)

In the above example, the `&s1` syntax creates a reference to the value `s1`, but does not own it. Because the function does not own it, the value that the reference points to will not be dropped when the function has completed.

In the `calculate_length` function, the `s` parameter declares the type to be `&String`. This is to say that the s parameter is required to be a reference to a `String.`

The action of creating a reference is known as `borrowing`. Things that are borrowed are not able to be modified.

E.g.

```rust
fn main() {
    let s = String::from("hello");

    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world") 
    // cannot borrow `some_string` as mutable, as it is behind a `&` reference
}
```

Like variables, that are immutable by default, references are as well.

### Mutable References 

To allow for references to be modified, the `mut` keyword must be added before the type, and after the ampersand, like so: `&mut String`.

You can only have one mutable reference at a time. Any code that has two mutable references to the same value will fail.

The restriction that prevents multiple mutable references at the same time allows for controlled mutation. The restriction prevents data races at compile time. A data race can occur when:

- Two or more pointers access the same data at the same time

- At least one of the pointers is being used to write the data

- There's no mechanism being used to synchronize access to the data

Data races causes behaviour that can be unexpected, and hard to diagnose and fix. Rust does not compile code with data races.

Rust enforces a rule that disallows combining mutable and immutable references. Multiple immutable references are allowed.

A references scope begins from where the reference is introduced, and ends at the last time that it is used.

### Dangling References

Dangling pointers are pointers that reference a location in memory that may have been given to someone else. These do not exist in Rust, as at compile time it is checked to see whether or not the reference should exist.

## The Slice Type

The slice type allows for a reference to be made to a sequence of elements, in a collection, rather than the whole collection. As slices are references, they do not have ownership.

The following example is based on a function that takes a string of words seperated by spaces as a parameter, abd returns the first word in that string. If a space isn't found, the entire string is returned.

```rust
fn first_word(s: &String) -> usize {

}
```

In this function declaration, the function takes in a reference to a string as a parameter, and returns the index of the end of the first word.

The full function looks like this:

```rust
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}
```

The first thing that this function does is converts the string to an array of bytes, using the `as_bytes` method. This is to allow for each element of the string to be checked in order to determine whether it is a space or not.

The next thing that this function does, is creates an iterator over the array of bytes, using the `iter` method. This method returns each element in a collection. The `enumerate` method then wraps the result and returns each element as part of a tuple.

The first element that is returned is the index, or `i`, and the second item that is returned is a reference to the element itself.

Then, in the for loop, if the byte, or reference to the element is equal to a space, the index is returned.

If a space is not found, the length of the string is returned.

### String Slices

A string slice is a reference to a part of a String. Slices are used so that the string as a whole does not have to be referenced in order to retrieve a part of a string.

String slices look like:

```rust
let example_string = String::from("my name is zak");

let my = &example_string[0..3];

let name = &example_string[4..8];
```

In a slice, a reference to the string that is being sliced is put next to a pair of square brackets, in which the first variable is the starting index, and the second being the ending index. The ending is equal to one more than the ending point of the slice.

Internally, slices are stored with a pointer that points to the starting index of the slice, and the length of the slice.

![Slice in memory](https://doc.rust-lang.org/book/img/trpl04-06.svg)

The first_word function can now be changed to return a slice. The type for string slices is `&str`.

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, item) in bytes.iter().enumerate() {
        item == b' ' {
            return &s[0..i]
        }
    }

    &s[..]
}
```

### String Literals as Slices

String literals are stored directly inside the binary. The type is `&str` - it is a slice that points directly toward where the string is stored in the binary. String literals are immutable for this reason; `&str` is an immutable reason. 

### Other Slices 

Slices can also be used on other types, such as Integers.

```rust

let array = [0, 1, 2, 3];

let slice = &array[0..2];

assert_eq!(slice, &[0, 1, 2]);