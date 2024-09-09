---
title: "On Rust's Tooling"
date: 2024-09-08T23:11:04-05:00
tags: ["rust"]
cover:
  image: images/b5ada72d-cd98-456c-878d-d9cb0c8f9b25.webp
summary: "Rust's tooling is really admirable."
---
Rust's tooling is really admirable.

When I wanted to make the middle light blink on a Micro:Bit V2, all I had to do to get my Rust code to compile was find the correct compile target and install it with a single command.
```sh
$ rustup target install thumbv7em-none-eabihf
```

When I wanted to convert a simple videogame from desktop to WASM, all I had to do to get my Rust code to compile was find the correct compile target and install it with a single command (and add some environment variables for compiling the dependent C library).
```sh
$ rustup target add wasm32-unknown-emscripten
```

I've now written code meant to run on ARM laptops, x64 servers, web browsers, and embedded devices using the same programming language, without the frustration of having to manage my memory model manually (say that 10 times as fast) or resort to some variation of JavaScript.

