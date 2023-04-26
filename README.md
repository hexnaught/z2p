# Zero to Production Rust Book by Luca Palmieri

<!-- Current Commit @ < 3.7 -->

This repository is following along the **'Zero2Prod'** rust book written by **Luca Palmieri**. You can find the book and information about the author [here](https://www.zero2prod.com/).

This readme will likely end up being a collection of points/notes to highlight from the book or research that aren't strictly to do with 'writing the application code'... More around project structure, practice, tooling, things to remember, etc.

## Optimisations on compilation and our inner development loop

- Zero2Prod, 1.4.1
- https://nnethercote.github.io/perf-book/compile-times.html

### Faster linking

Using alternative linker such as `lld` by the LLVM project for Windows and Linux, `zld` on MacOS. See `.cargo/config.toml` for information on setting these up and platform specific configurations. There is a newer linker called `mold` but at the time of writing the zero2prod book, it seemed a bit early for the author to include it.

<!-- TODO: Check out `mold` -->

### Using `cargo-watch`

> We can also mitigate the impact on our productivity by reducing the perceived compilation time - i.e. the
time you spend looking at your terminal waiting for cargo check or cargo run to complete.

```sh
cargo install cargo-watch
```

You can use cargo watch to perform cargo commands on file change, this includes chaining commands - such as check, test and run.

```sh
cargo watch -x check
cargo watch -x check -x test -x run
```

### Using `cargo tarpaulin`

`tarpaulin` is a cargo subcommand that measures code coverage, while not a great metric on quality, it is a good way to see untested areas of the codebase.

```sh
# At the time of writing tarpaulin only supports
# x86_64 CPU architectures running Linux.
cargo install cargo-tarpaulin
```

```sh
cargo tarpaulin --ignore-tests
```

### Linting rust code

You can lint rust code using `clippy`, a component of the `rustup` toolchain.

```sh
rustup component add clippy
```

```sh
# Running clippy
cargo clippy
# Running clippy and exiting with non-zero code on warnings, useful for CI
cargo clippy -- -D warnings
```

Sometimes there can be linting issues clippy detects that aren't actually problematic, in those cases we can instruct clippy to ignore that code block with the following attribute: `#[allow(clippy::lint_name)]`.

There is also a `clippy.toml` config that can be added to the project as well as project level directives such as `#![allow(clippy::lint_name)]`.


### Formatting rust code

```sh
rustup component add rustfmt
```

```sh
# Running cargo fmt
cargo fmt
# Running cargo fmt checks, useful for CI
cargo fmt -- --check
```

You can tune to the formatter to fit the project by adding a `rustfmt.toml` configuration file.

### Vulnerability scanning of dependencies

There is an advisory database maintained by 'The Rust Secure Code' working group, using the `cargo-audit` subcommand we can check if any vulnerabilities have been reported in any of the dependencies our project has.

```sh
cargo install cargo-audit
```

```sh
cargo audit
```

## Other tooling

### Cargo Expand

`cargo-expand` is a cargo subcommand that expands all macros and code generation without passing the output to the compiler, which allows us to inspect and debug the code that we use but otherwise would not see.

```sh
cargo install cargo-expand

cargo expand
```
