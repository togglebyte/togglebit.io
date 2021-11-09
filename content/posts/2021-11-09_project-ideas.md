+++
title = "Project ideas"
date = 2021-11-09
+++

## URL shortener

A url shorterner, where you can post a long url to a web server, which returns a
short url. Visiting the short url should redirect to the long version.

### Libraries

* Blocking: https://crates.io/crates/simple-server
* Async: https://crates.io/crates/axum

### Reading

* The Book has a
  [chapter](https://doc.rust-lang.org/book/ch20-00-final-project-a-web-server.html)
  on building a web server


### Outline

Decide on sync or async.

* [ ] Create a web server
    * [ ] Store urls (hash map, database, flat file etc.)
    * [ ] Lookup urls
    * [ ] Redirect urls


## Secret Santa Picker

### Libraries

* [Rand](https://crates.io/crates/rand/)

### Reading

* [Vec](https://doc.rust-lang.org/std/vec/struct.Vec.html)
* [Random choice from a slice](https://docs.rs/rand/0.8.4/rand/seq/trait.SliceRandom.html#tymethod.choose)

### Outline

* [ ] Create a list of names
* [ ] Choose a random name
* [ ] Print name to screen


## Dictionary / Translator

A command line application that looks up a word passed as argument,
and either translates or provides a description of the word (translator vs
dictionary).

### Libraries

No libraries needed for such a project

### Reading

* [Args](https://doc.rust-lang.org/std/env/fn.args.html)
* [read_to_string](https://doc.rust-lang.org/std/fs/fn.read_to_string.html)
* [File](https://doc.rust-lang.org/std/fs/struct.File.html)

### Outline

* [ ] Get argument(s) from command line
* [ ] Fill a dictionary with words
* [ ] Look up the correct word if one is matching the input

