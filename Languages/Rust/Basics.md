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

Tuples are basically data structures which can store values of different types. *The length of a tuple isn't dynamic and types of values at positions need to be specified beforehand (or are inferred)*.

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