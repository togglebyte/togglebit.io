+++
title = "Epoll continued"
date = 2020-12-08
draft = true
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
        ptr::null_mut(), // event is not required since kernel 2.6.9
    );
```

## Events

TODO: finish this.
See `man epoll_ctl`

Edge triggered: only triggers events when the underlying file descriptor
changes.
Without ONESHOT multiple events might still be triggered for the same file
descriptor.

```rust
libc::EPOLLET
```

When passed to epoll, the selected file descriptor is registered for read events.
When received from epoll through `epoll_wait` the file descriptor can be read from.

```rust
libc::EPOLLIN
```

When passed to epoll, the selected file descriptor is registered for write events.
When received from epoll through `epoll_wait` the file descriptor can be written
to.

```rust
libc::EPOLLOUT
```

Urgent read

```rust
libc::EPOLLPRI
```

Error on the associated file descriptor.
This is set by epoll.

```rust
libc::EPOLLERR
```

Hang up the file descriptor.

```rust
libc::EPOLLHUP
```

Socket closed or writing half shut down.
Useful to be notified whether the socket was closed or not: `EPOLLIN | EPOLLRDHUP`

```rust
libc::EPOLLRDHUP
```

```rust
libc::EPOLLWAKEUP       // Prevents system for sleep (what is a system here?)
```

```rust
libc::EPOLLONESHOT      // Without one-shot it's possible to receive multiple events
```

Looks like it will only wake up the epoll file descriptor where this event's
file descriptor was registered, if the event was registered with `EPOLLEXCLUSIVE`.

```rust
libc::EPOLLEXCLUSIVE    //
```

## EventFd

See `man eventfd` and `libc::eventfd`
