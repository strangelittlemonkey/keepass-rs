# keepass

[![Crates.io](https://img.shields.io/crates/v/keepass.svg)](https://crates.io/crates/keepass)
[![Documentation](https://docs.rs/keepass/badge.svg)](https://docs.rs/keepass/)
[![Build Status](https://travis-ci.org/sseemayer/keepass-rs.svg?branch=master)](https://travis-ci.org/sseemayer/keepass-rs)
[![codecov](https://codecov.io/gh/sseemayer/keepass-rs/branch/master/graph/badge.svg)](https://codecov.io/gh/sseemayer/keepass-rs)

KeePass .kdbx database file parser for Rust

## Example
```rust
extern crate keepass;

use keepass::{Database, Node, Result, Error};
use std::fs::File;

fn main() -> Result<()> {
    // Open KeePass database
    let path = std::path::Path::new("tests/resources/test_db_with_password.kdbx");
    let db = Database::open(
        &mut File::open(path)?,         // the database
        Some("demopass"),               // password
        None                            // keyfile
    )?;

    // Iterate over all Groups and Nodes
    for node in &db.root {
        match node {
            Node::Group(g) => {
                println!("Saw group '{0}'", g.name);
            },
            Node::Entry(e) => {
                let title = e.get_title().unwrap();
                let user = e.get_username().unwrap();
                let pass = e.get_password().unwrap();
                println!("Entry '{0}': '{1}' : '{2}'", title, user, pass);
            }
        }
    }

    Ok(())
}
```

## Installation
Add the following to the `dependencies` section of your `Cargo.toml`:

```
[dependencies]
keepass = "*"
```

**Performance note:** Please set the `RUSTFLAGS` environment variable when compiling to enable CPU-specific optimizations (this greatly affects the speed of the AES key derivation):

```bash
export RUSTFLAGS='-C target-cpu=native'
```

For best results, also compile in Release mode.

Alternatively, you can add a `.cargo/config.toml` like in this project to ensure that rustflags are always set.

## [Documentation](https://docs.rs/keepass)

## License
MIT
