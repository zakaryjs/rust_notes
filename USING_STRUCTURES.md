# Using Structs to Structure Related Data

## Defining and Instantiating Structs

Structs are similar to JavaScript classes.

Structs are similar to tuples as they both hold multiple related values. The data inside a struct can be different types. Inside a struct, there are key value pairs, which define what the data is, and what the data type is. The order of these pairs does not matter.

A struct is first declared using the `struct` keyword, followed by the struct name. The key pairs are stored inside curly brackets, otherwise known as fields.

An example User account:

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

A struct can then be used by creating an instance of it. This is done by defining a variable that is equal to the name of the struct, followed by curly brackets which contain the fields that were defined when the struct was created. 

A struct is like a blueprint for a new type.

```rust
let new_user1 = User {
    active: true,
    username: String::from("randomusername"),
    email: String::from("randomemail@mail.com"),
    sign_in_count: 1,
}
```

Like JavaScript, values can be retrieved from a struct using dot notation. If the struct is mutable, the values can also be changed this way.

```rust
let mut mutable_user = User {
    active: true,
    username: String::from("jimmy"),
    email: String::from("anotheremail@mail.com"),
    sign_in_count: 4,
};

mutable_user.email = String::from("newemail@mail.com");
```

Structs are either mutable or immutable, certain fields cannot be different from one another. Functions can be used to create structs.

```rust
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username: username,
        email: email,
        sign_in_count: 1,
    }
}
```