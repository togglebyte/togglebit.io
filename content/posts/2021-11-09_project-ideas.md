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
