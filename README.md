# Reustmann - a Von Neumann architecture

[![Build Status](https://travis-ci.org/Kerollmops/reustmann.svg?branch=master)](https://travis-ci.org/Kerollmops/reustmann)

Reustmann is a Von Neumann architecture in Rust.
I was inspired by [the dave miller Iota machine](http://www.millermattson.com/dave/?p=174).

I just recreate it in **Rust**.

The Reustmann [Documentation](https://kerollmops.github.io/reustmann-doc/reustmann/reustmann)

## How to

The `hello_world.rm` program
```text
Gp..OOOOOOOOOOOOHTFello World!
```

First create a program, from a file for example
```rust
extern crate reustmann;

use std::fs::File;
use reustmann::Program;

let ignore_last_newline = true;
let file = File::open("./hello_world.rm").unwrap();

// it accept anything implementing the `std::io::Read` trait
let program = Program::new(file, ignore_last_newline).unwrap();
```

Then use this program to fill the interpreter memory
```rust
use reustmann::Interpreter;

let arch_length = 50; // memory length
let arch_width = 8; // word size

let mut interpreter = Interpreter::new(arch_length, arch_width).unwrap();
interpreter.copy_program_and_reset(&program).unwrap();
```

Launch it, launch it now !!!
```rust
use reustmann::Statement;
use reustmann::instruction::op_codes;
use std::io::{empty, stdout};
// use std::io::sink; // for no output

let mut input = empty(); // no input data needed
let mut output = stdout(); // output on the standard

loop {
    // each interpreter step return a statement
    // while no `HALT` statement is found, we continue
    match interpreter.step(&mut input, &mut output) {
        Statement(op_codes::HALT, _) => break,
        _ => ()
    }
}
```

You can have debug informations at any moment
```rust
// put this in the previous match, at the good place ;)
println!("{:?}", interpreter.debug_infos());
```
