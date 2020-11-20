## 高性能IO模型

首先，我们通常说，Redis是单线程，主要是指Redis的网络IO和键值对读写是由一个线程来完成的，这也是Redis对外提供键值存储服务的主要流程。但Redis的其他功能，比如持久化、异步删除、集群数据同步等，其实是由额外的线程执行的。所以，严格来说，Redis并不是单线程。

### 为什么用单线程

为了更好地理解为什么用单线程，就要先了解多线程的开销。

使用多线程，可以增加系统吞吐率，或是可以增加系统扩展性。的确，对于一个多线程的系统来说，在有合理的资源分配的情况下，可以增加系统中处理请求操作的资源实体，进而提升系统能够同时处理的请求数，即吞吐率。但是，注意，在采用多线程后，如果没有良好的系统设计，就会造成下面的情况：在刚开始增加线程数时，系统吞吐率会增加，但是，再进一步增加线程时，系统吞吐率就增长迟缓了，有时甚至还会出现下降的情况。

这个现象的关键在于，系统中通常会存在被多线程同时访问的共享资源，比如一个共享的数据结构。当有多个线程要修改这个共享资源时，为了保证共享资源的正确性，就需要有额外的机制进行保证，而这个额外的机制，就会带来额外的开销。

这就是多线程编程模式面临的共享资源的并发访问控制问题。并发访问控制一直是多线程开发中的一个难点问题，如果没有精细的设计，比如说，只是简单地采用一个粗粒度互斥锁，就会出现不理想的结果：即使增加了线程，大部分线程也在等待获取访问共享资源的互斥锁，并行变串行，系统吞吐率并没有随着线程的增加而增加。

### 单线程Redis为什么快

通常来说，单线程的处理能力要比多线程差很多，但是Redis却能使用单线程模型达到每秒数十万级别的处理能力，这其实是Redis多方面设计选择的一个综合结果。一方面，Redis的大部分操作在内存上完成，再加上它采用了高效的数据结构，例如哈希表和跳表，这是它实现高性能的一个重要原因。另一方面，就是Redis采用了多路复用机制，使其在网络IO操作中能并发处理大量的客户端请求，实现高吞吐率。

### Redis基本IO模型与阻塞点

首先，我们要弄明白网络操作的基本IO模型和潜在的阻塞点。Redis采用单线程进行IO，如果线程被阻塞了，就无法进行多路复用了。以Get请求为例，为了处理一个Get请求，需要监听客户端请求（bind/listen），和客户端建立连接（accept），从socket中读取请求（recv），解析客户端发送请求（parse），根据请求类型读取键值数据（get），最后给客户端返回结果，即向socket中写回数据（send）。

在这里的网络IO操作中，有潜在的阻塞点，分别是accept()和recv()。当Redis监听到一个客户端有连接请求，但一直未能成功建立起连接时，会阻塞在accept()函数这里，导致其他客户端无法和Redis建立连接。类似的，当Redis通过 recv()从一个客户端读取数据时，如果数据一直没有到达，Redis也会一直阻塞在recv()。这就导致Redis整个线程阻塞，无法处理其他客户端请求，效率很低。不过，幸运的是，socket网络模型本身支持非阻塞模式。

### Socket非阻塞模式

在socket模型中，不同操作调用后会返回不同的套接字类型。socket()方法会返回主动套接字，然后调用listen()方法，将主动套接字转化为监听套接字，此时，可以监听来自客户端的连接请求。最后，调用accept()方法接收到达的客户端连接，并返回已连接套接字。

| 调用方法 | 返回套接字类型 | 非阻塞模式 | 效果                |
| -------- | -------------- | ---------- | ------------------- |
| socket() | 主动套接字     |            |                     |
| listen() | 监听套接字     | 可设置     | accept()非阻塞      |
| accept() | 已连接套接字   | 可设置     | send()/recv()非阻塞 |

针对监听套接字，可以设置非阻塞模式：当Redis调用accept()但一直未有连接请求到达时，Redis线程可以返回处理其他操作，而不用一直等待。但是，要注意的是，调用accept()时，已经存在监听套接字了。虽然Redis线程可以不用继续等待，但是要有机制继续在监听套接字上等待后续连接请求，并在有请求时通知Redis。类似的，也可以针对已连接套接字设置非阻塞模式：Redis调用recv()后，如果已连接套接字上一直没有数据到达，Redis线程同样可以返回处理其他操作。也需要有机制继续监听该已连接套接字，并在有数据达到时通知Redis。

### 基于多路复用的高性能I/O模型

Linux中的IO多路复用机制是指一个线程处理多个IO流，就是我们经常听到的select/epoll机制。简单来说，在Redis只运行单线程的情况下，该机制允许内核中，同时存在多个监听套接字和已连接套接字。内核会一直监听这些套接字上的连接请求或数据请求。一旦有请求到达，就会交给Redis线程处理，这就实现了一个Redis线程处理多个IO流的效果。

下图就是基于多路复用的Redis IO模型。图中的多个FD就是刚才所说的多个套接字。Redis网络框架调用epoll机制，让内核监听这些套接字。此时，Redis线程不会阻塞在某一个特定的监听或已连接套接字上，也就是说，不会阻塞在某一个特定的客户端请求处理上。正因为此，Redis可以同时和多个客户端连接并处理请求，从而提升并发性。

![](E:\GongZuoQu\KTZhiShiKu\TuPian\JiKeShiJian\Redis\ShuJuJieGou_img06.jpg)

为了在请求到达时能通知到Redis线程，select/epoll提供了基于事件的回调机制，即针对不同事件的发生，调用相应的处理函数。回调机制基于事件工作，select/epoll一旦监测到FD上有请求到达时，就会触发相应的事件。这些事件会被放进一个事件队列，Redis单线程对该事件队列不断进行处理。这样，Redis无需一直轮询是否有请求实际发生，这就可以避免造成CPU资源浪费。同时，Redis在对事件队列中的事件进行处理时，会调用相应的处理函数，这就实现了基于事件的回调。因为Redis一直在对事件队列进行处理，所以能及时响应客户端请求，提升Redis的响应性能。

即使应用场景中部署了不同的操作系统，多路复用机制也是适用的。因为这个机制的实现有很多种，既有基于Linux系统下的select和epoll实现，也有基于FreeBSD的kqueue实现，以及基于Solaris的evport实现。

### 基本IO模型的其他瓶颈

第一种是单一耗时操作，比如，复杂度是O(N)的那些操作在N很大的时候，就会出现阻塞，后面的请求都必须等这个操作执行完才能继续。再比如，还有一些隐藏在主线程中的功能，比如key过期，如果是大批量的key过期，Redis也会需要耗时删除这些key。另外还有bigkey的操作，不过Redis在4.0推出了lazy-free机制，把bigkey释放内存的耗时操作放在了异步线程中执行，降低对主线程的影响。

第二种是并发量非常大的时候，单线程读写客户端IO数据存在性能瓶颈，虽然采用IO多路复用机制，但是读写客户端数据依旧是同步IO，只能单线程依次读取客户端的数据。Redis在6.0推出了多线程，可以在高并发场景下利用CPU多核多线程读写客户端数据。当然，只是针对客户端的读写是并行的，每个命令的真正操作依旧是单线程的。