## Client-Server message sharing
The idea is to handle multiple live, concurrent connections on a single machine with minimum CPU usage. It does that by using thread pooling and [EPOLL](https://en.wikipedia.org/wiki/Epoll#:~:text=epoll%20is%20a%20Linux%20kernel,possible%20on%20any%20of%20them.)

## How it works
All the file descriptors are added to the epoll structure. When a file descriptor has a new connection or some data to read, that fd is added in the ready state (by the kernel in the background). When the epoll_wait() is called, it returns the number of file descriptors that are in the ready state and the file descriptors are added in the epoll_arr. If the fd is s_socket then it means there is a new connection so a new client is added. If the fd is a client and there is data to read then a new task is added in the QUEUE. All the threads are waiting for a task to get. `Thundering Herd` problem is solved by using mutex and conditional variables. If the fd is a client and it has lost the connection then the fd is removed from the epoll structure.

## WIP
1. Auto generate maximum threads based on hardware
2. Currently it is not a real application, just a server-client connection

## Useful
* [A million web socket connections](https://www.youtube.com/watch?v=LI1YTFMi8W4)
* [Fix EPOLL](https://idea.popcount.org/2017-02-20-epoll-is-fundamentally-broken-12/)