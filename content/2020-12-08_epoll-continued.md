+++
title = "Epoll continued"
date = 2020-12-08
+++


## Epoll ctl operations

It is vital to re-register, using `libc::EPOLL_CTL_MOD` when using epoll in
one-shot mode.

Example adding a file descriptor.

```rust
    epoll_ctl(
        epoll_fd,
        libc::EPOLL_CTL_ADD
        fd,
        &mut event as *mut libc::epoll_event
    );
```

Updating a file descriptor is vital when using epoll in one-shot mode.

Example updating a file descriptor.

```rust
    epoll_ctl(
        epoll_fd,
        libc::EPOLL_CTL_MOD
        fd,
        &mut event as *mut libc::epoll_event
    );
```

Example removing a file descriptor.

```rust
    epoll_ctl(
        epoll_fd,
        libc::EPOLL_CTL_DEL
        fd, // removes this fd
        ptr::null(), // event is not required since kernel 2.6.9
    );
```

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
