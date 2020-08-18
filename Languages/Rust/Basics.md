# Rust

Rust is a systems programming language like C and it is memory safe.

> Memory safe language is basically a language which don't have memory related bugs / vulnerabilities.

## Primitives

In rust there are 2 types of primitives

### Scalar

These are basic types in Rust. Examples include:

- integers (both signed and unsigned)
- char
- floating point numbers and
- Boolean
- special type which is unit type(?) which has a empty tuple as only possible it's value.

### Compound

- Array - contains values of same type.
- Tuple - Can contain values of different type.

#### Defining variables in Rust

This is a bit different than most languages. The **let** keyword is used to define variables. Type of a variable is inferred by the rust compiler if type not mentioned explicitly. There is no special notation to infer type.

Ex.

```
let twitchisawesome: bool = true; // This is defining a variable with explicit type
let number = 32; // Here type is inferred
```

> A integer by default is of type i32 and floating point number is of type f64.

Need to learn about **mut** keyword.

#### Tuples

Tuples are basically data structures which can store values of different types. _The length of a tuple isn't dynamic and types of values at positions need to be specified beforehand (or are inferred)_.

Accessing values in a tuple can be done using `.` followed by the value index.

Ex.

```rust
let tuple = (1,1.3,false);
tuple.0 //1
let nested_tuple = ((1,3,"world"),1,6,false);
(nested_tuple.0).2; //"world"
println!("{:?}",tuple)
```

Also a tuple can be printed only if it is below a specific length.

> A single valued tuple needs to have a comma at the end else it will be interpreted as a scalar and not a tuple.

#### Arrays and Slices

Arrays are contiguous blocks of memory which hold values of same type.Size of an array is fixed at compile time and cannot be changed. While initializing a new array you need to define all values in the array.

Slices on the other hand are parts of an array and can have a dynamic size. Slices point to a underlying array and store just an address. An array can be passed as a slice without any type conversions.

Ex.

```rust
let nums: [i32; 5] = [1, 2, 3, 4, 5]; // Need to have 5 elements in the array as defined.
&nums // This is a slice
&nums[2 .. 4] // Creates a slice from index 2 to 3
```

## Compound Types

#### Structures

Rust has 3 types of structures

- Tuple Struct
- C Struct
- Unit Struct

##### Tuple Structs

These are tuples with a custom name type. Useful for creating a custom type based on tuple.

##### C Struct

These are structures that are similar to the ones in other languages. Accessing values of fields in the structure can be done by using `.` symbol for example `tesla.chasisNumber` will return a i32 value.

Instantiating a structure is done using curly braces and defining value of each property against it's name or you can pass variables with same name as struct fields.

A structure can be a part of another structure i.e. nested structures.

##### Unit Structs

Are basically non-field structs. Are used for generics need to explore more to understand a its use.

Ex.

```rust
struct ContactNumbers(i32, i32);
let contactNumber = ContactNumber(12345,123456); // Here the type of variable contactNumber is inferred.

struct Car {
    chasisNumber:i32,
    mileage:f32,
}

let tesla1:Car = Car{chasisNumber:12345, mileage:63.4};

// This is something i find cool about rust structs

let tesla2:Car =  Car {chasisNumber:12367, ..tesla1}; // Logging tesla2 will print mileage as 63.4 which is borrowed from tesla1

struct Unit; // This will be used for generics

```

#### Enums

I am unsure on what to write here so this is a [link to the doc](https://doc.rust-lang.org/stable/rust-by-example/custom_types/enum/enum_use.html) that I referred while learning rust.

## Variable Bindings

#### Mutability

Variables defined with a **let** keyword are immutable, so once a value is assigned to them changing it is not possible. One way is to re-define the same variable with desired value. Let is like a constant but is computed at runtime. Constants on the other hand are computed at compile time. So if a program has a value which is used frequently const can be used there.

Mutable variables can be defined with the **mut** keyword.

Ex.

```rust
let a = 1; // a is now immutable
a = 2; // will throw an error

let b;
b = 3 // This is valid.

let mut c = 4;
c = 5; // The value of c will be changed to 5
```

#### Scopes and Shadowing

Variables are block scoped.

Ex.

```rust
fn main(){
    let a = 1;
    {
        let a = 2; // Inner scope
    }
    let a = 3; // Same scope but shadows the previous definition.
}
```

#### Freezing

Ex.

```rust

fn main(){
    let mut a = 1;
    {
        let a = 2;
        a =3; // Error as a is now immutable
    }
    a = 5; // Okay as it's outside the scope.
}

```

## Types

#### Casting

No implicit casting allowed. Type casting can de done explicitly using `as` keyword. There are some rules to type casting that are C like.

Ex.

```rust
let decimal = 65.1233;

let number = decimal as u8; // this tries to fit the value into u8, if not then mods it my max of u8 so that it fits.

let symbol = number as char;
let decymbol = decimal as char; // not allowed.

let unumber = -200i16 as u16 // Converted to positive if doesn't fit then mod value by max of destination type.
```

#### Literals

The datatype of a literal can be specified by suffixing it with an appropriate datatype.

Ex.

```rust
let x = 50i32;
```

### Conversions

Supports custom type conversions using traits.Rust believes that if you are able to convert typeA into typeB you should be able to convert typeB to typeA. Rust allows type conversions of custom types via traits. Below are some common traits.

#### From and Into traits

`From` and to `Into` traits are required for converting types to a particular type. For the conversion to work, a `from` function on the custom datatype has to be specified. One data type can have many `from` functions. `into` uses the from function for the conversion.

Ex.

```rust
struct A {
    value: i32
}

//This from function will be used by the from and into trait implicitly for type conversion.

impl From<i32> for A {
    fn from(val: i32) -> Self {
        A {value: val};
    }
}

// Both of these type conversions convert a i32 to a compound type A.
fn main() {
    let b = A::from(30);
    let c : A = 43i32.into();
}
```

#### TryFrom and TryInto

These are used for type conversions in cases special cases where the type conversion can fail.

```rust
struct DivBy5{
    value:i32
}

impl TryFrom<i32> for DivBy5 {
    type Error = ();
    fn try_from(val:i32) -> Result<Self, Self::Error> {
        if val % 5 == 0 {
            Ok(DivBy5{value:val})
        } else {
            Err(())
        }
    }
}

fn main(){
    let result: Result<DivBy5, ()> = 8i32.try_into();
    println!("{:?}", result);
}
```

#### To and from String

Doc link [here](https://doc.rust-lang.org/stable/rust-by-example/conversion/string.html)

_Note to self: Understand the use of a semicolon in rust_

### Expressions

Expressions are terminated by a semicolon. A variable definition or a declaration are expressions. But also, blocks are expressions. So you can assign a code block as a value to a variable.

The one rule that you need to keep in mind while assigning blocks to variables is that, if the last statement in the block ends with `;` then the value of the variable is a unit.
Else it will be the value returned by the last statement.

Ex.

```rust

let a = {
    let c = 2;
    let b = 3;
    c + b
} //Value of a is 5

let d = {
    let z = 3;
} // Value of d is ()

```

### Control Flows

#### if-else

No parentheses surrounding the boolean expression. Also same type of value needs to be returned by every if-else branch.
