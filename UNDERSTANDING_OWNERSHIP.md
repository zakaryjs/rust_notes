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