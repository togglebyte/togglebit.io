+++
title = "Setting up a personal crates repository using Git"
date = 2024-08-26
draft = false
+++

For this post Github is used, but any Git repository could be used.

## Create the crate

The first crate is to have something that can be added to the registry.
This is to confirm that it's working, and for the sake of this post the crate
will be referred to as `greeter`.

An example (which is used for the remainder of this post) would be a library
with a public function: 

```rust
pub fn greet() { 
    println!("hello");
}
````

To generate a `.crate` file run `cargo package`.
The package will be located in `target/package/greeter-0.1.0.crate`.

The checksum can be generated with `sha256sum greeter-0.1.0.crate`, assuming
it's available.

## Create the Registry

Create and initialise a git repository.
For the sake of this post the repository will be named `pubreg`.

The repository should contain a `config.json` file, along with index files.
An index file contains version and meta information about a
crate, so each crate has its own index file with the same name as the crate.

Given a crate by the name `greeter` there should be two files:
* `greeter` the index file
* `greeter.crate` the crate

First step is to define the `config.json` file that describes the download path
for each crate.

This file should only contain a single entry:

```json
{
    "dl": "https://raw.githubusercontent.com/<username>/pubreg/main/crates/{crate}/{version}.crate",
    "api":null,
    "auth-required":false
}
```

The `dl` value describes how to create the url for each crate.

{% note() %}
Note that `api` and `auth-required` are not in use here.
For more information about these settings see: [Index configuration](https://doc.rust-lang.org/cargo/reference/registry-index.html#index-configuration).
{% end %}

Each crate should be uploaded to the `crates` directory, under a sub directory
with the same name as the crate, and finally the crate it self should have the
version name as the filename.

So the `greeter` crate should, in the git repository, be located at:
`crates/greeter/0.1.0.crate`

Each crate needs an index file with the same name as the crate, so for 
the `greeter` crate the index file would look as follows:

```
{"name": "greeter","vers": "0.1.0","deps": [],"cksum": "2044d936f85f45c316aecc9159712c4a10c1e10413e6491eee0282825f499cce","features": {},"yanked": false,"links": null}
```

Each line in the index file represents a version of the crate, so the example
above is for one version of the greeter crate, and can not take up more than
one line in the index file.

The file structure for index files are as follows:

The following paragraph is taken directly from [Index files](https://doc.rust-lang.org/cargo/reference/registry-index.html#index-files):

> * Packages with 1 character names are placed in a directory named `1`.
> * Packages with 2 character names are placed in a directory named `2`.
> * Packages with 3 character names are placed in the directory `3/{first-character}` where `{first-character}` is the first character of the package name.
> * All other packages are stored in directories named `{first-two}/{second-two}` where the top directory is the first two characters of the package name, and the next subdirectory is the third and fourth characters of the package name. For example, cargo would be stored in a file named `ca/rg/cargo`.

For more information see [Index files](https://doc.rust-lang.org/cargo/reference/registry-index.html#index-files).

So for the `greeter` crate the index file would be located in `gr/ee/` under the
name `greeter`.

## Use the Registry

Create a project that will make use of the registry.

Create a directory in the root of your project, named `.cargo`, and add a
`config.toml` file to that directory: `.cargo/config.toml`.

Inside the `config.toml` file specify the registry settings:

```toml
[registries]
my-registry = { index = "https://github.com/<username>/pubreg/" }
```

Adding `greeter` as a dependency to `Cargo.toml`:

```toml
[dependencies]
greeter = { version = "0.1.0", registry = "my-registry" }
```

{% note() %}
Note the `registry` attribute.
{% end %}

## Sources

* https://doc.rust-lang.org/cargo/reference/running-a-registry.html
* https://github.com/integer32llc/margo
