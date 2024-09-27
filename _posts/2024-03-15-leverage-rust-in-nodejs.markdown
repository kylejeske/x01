---
layout: post
title: "Leveraging napi-rs to Integrate Rust in Node.js"
date: 2024-09-27
categories: [Node.js, Rust, napi-rs]
---

When developing high-performance applications in Node.js, there are times when JavaScript's single-threaded nature and the limitations of the V8 engine can become bottlenecks. By leveraging Rust for CPU-intensive tasks while keeping Node.js for I/O-bound operations, you can significantly improve the performance of your application. 

One of the best tools for bridging Rust and Node.js is [`napi-rs`](https://github.com/napi-rs/napi-rs), a library that provides bindings between Rust and the Node.js N-API. Using `napi-rs`, you can write native modules in Rust that are safe, performant, and work across multiple versions of Node.js.

### Why Choose napi-rs?

- **Cross-Version Compatibility**: Since `napi-rs` relies on the N-API, it works across different versions of Node.js without needing to adapt to changes in the V8 engine.
- **Rust’s Safety and Performance**: Rust’s memory safety guarantees and performance benefits make it a solid choice for handling CPU-bound tasks like data manipulation, encryption, or heavy computation.
- **Async Support**: `napi-rs` simplifies handling asynchronous operations between Rust and JavaScript, which is vital for non-blocking Node.js applications.

### Setting Up napi-rs

Let’s walk through a simple example where we write a Rust function to add two numbers and expose it to Node.js using `napi-rs`.

#### Step 1: Setting Up a Rust Project

First, create a new Rust library project:

```bash
cargo new --lib my-rust-module
cd my-rust-module
```

In your Cargo.toml, add the following dependencies:

```toml
[dependencies]
napi = { version = "3.0", features = ["napi3"] }
napi-derive = "3.0"
```

This will set up napi-rs to work with Node.js and allow us to generate bindings for our functions.

#### Step 2: Writing Rust Code
Now, let's create a basic function in Rust that adds two numbers and expose it to Node.js.

```rust
// src/lib.rs

use napi::bindgen_prelude::*;
use napi_derive::napi;

#[napi]
fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

Here, the #[napi] macro is used to expose the add function to JavaScript. This function takes two integers (a and b) and returns their sum.

Step 3: Building the Rust Code
Next, we need to build the Rust code into a format that Node.js can consume.

First, install @napi-rs/cli, a build tool to compile your Rust code into a Node.js module:

```bash
npm install -g @napi-rs/cli
```

Then, add the following to your Cargo.toml under the package.metadata section to define the build target for Node.js:

```toml
[package.metadata.napi]
name = "my_rust_module"
```

Finally, run the build command:
```bash
napi build --release
```

This will compile the Rust code into a .node binary file, which can be used as a native Node.js module.

#### Step 4: Using the Compiled Rust Code in Node.js
Now, you can use the compiled Rust code in your Node.js project like any other module.

Here’s the equivalent JavaScript code to use the add function:

```javascript
// index.js

const { add } = require('./index.node'); // Import the compiled Rust module

const result = add(5, 10);
console.log(`The result is ${result}`);
```

When you run this script, it will output:

```bash
The result is 15
```

#### Step 5: Generating TypeScript Definitions
To make the Rust module TypeScript-friendly, you can generate TypeScript definition files using napi-rs. This ensures that TypeScript projects consuming the module will have proper type annotations.

First, create a package.json file in your project and add the napi TypeScript bindings:
```bash
npm init -y
npm install @napi-rs/cli --save-dev
```

In your Cargo.toml, add the ts-bindings feature to generate type definitions:

```toml
[package.metadata.napi]
name = "my_rust_module"
ts-bindings = true
```

Then rebuild the project:

```bash
napi build --release
```

This will generate a .d.ts file along with the .node binary. You can now import the module with TypeScript types:

```typescript
// index.ts

import { add } from './index.node';

const result: number = add(5, 10);
console.log(`The result is ${result}`);
```

That's pretty cool huh.

Leveraging Rust with napi-rs allows you to harness the power and safety of Rust within your Node.js applications, making it easier to offload CPU-heavy tasks without compromising on performance. With napi-rs, you can easily expose Rust functions to JavaScript, all while maintaining cross-version compatibility with Node.js and taking advantage of TypeScript support for a seamless developer experience.