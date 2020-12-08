+++
title = "Epoll continued"
date = 2020-12-08
+++


## Events

TODO: finish this. 
See `man epoll_ctl`

```rust
libc::EPOLLET           // Edge triggered
libc::EPOLLIN           // Read available
libc::EPOLLOUT          // Write available
libc::EPOLLPRI          // Urgent read
libc::EPOLLERR          // Error on associated file descriptor
libc::EPOLLHUP          // Hang up on the file descriptor
libc::EPOLLRDHUP        // Socket closed or writing half shut down
libc::EPOLLWAKEUP       // 
libc::EPOLLONESHOT      // 
libc::EPOLLEXCLUSIVE    // 
```

## EventFd
See `man eventfd` and `libc::eventfd`
