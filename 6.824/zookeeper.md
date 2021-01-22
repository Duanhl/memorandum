# zookeeper

## 1. overview

## 2. ordering guarantees

1. `Linearizable writes`: all requests that update the state of ZooKeeper are serializable and respect precedence.
2. `FIFO client order`: all requests from a given client are executed in the order that they were sent by the client.

A-linearizability (asynchronous linearizability), allow a client to have multiple outstanding operations;

---
Q: 为什么写入是串行化，但是读取不是

A：为了性能。如果要提高读取的吞吐量，比如Nx 吞吐量的话，每一个节点，即使它是Follower，也需要能够提供独立的读取操作。但是从Follower上读取的话，不一定能够读取到已提交的数据，有可能是这个服务器没有在达成共识的分片上，也有可能这个这个服务器已经被分区，还有可能这个服务器在达成共识的分片上但是没有得到Leader的ACK。zk为每一个事务提供了一个递增的zxid，类似于raft的log index概念，Client缓存着上次读取的zxid，Client拒绝下一次读取到zxid小于保存的zxid的数据。zk对于Client的顺序保证只是保证每一个Client执行命令的数据不会后退，但是不保证能够读取到时序上最新的写入数据。两条保证还隐含了一种情况，如果Client的请求中有一条写入，即使Client是采用异步的批量操作，这个写入后面的读取会阻塞直到写入执行成功。

---

## 3. Examples of primitives

### Simple Locks without Herd Effect

```c
Lock
1 n = create(l + “/lock-”, EPHEMERAL|SEQUENTIAL)
2 C = getChildren(l, false)
3 if n is lowest znode in C, exit
4 p = znode in C ordered just before n
5 if exists(p, true) wait for watch event
6 goto 2

Unlock
1 delete(n)
```

### Read/Write Locks

```c
Write Lock
1 n = create(l + “/write-”, EPHEMERAL|SEQUENTIAL)
2 C = getChildren(l, false)
3 if n is lowest znode in C, exit
4 p = znode in C ordered just before n
5 if exists(p, true) wait for event
6 goto 2

Read Lock
1 n = create(l + “/read-”, EPHEMERAL|SEQUENTIAL)
2 C = getChildren(l, false)
3 if no write znodes lower than n in C, exit
4 p = write znode in C ordered just before n
5 if exists(p, true) wait for event
6 goto 3
```

## 4. ZooKeeper Implementation

Q: zk pipline

A: zk的流水线执行体现在两个方向，leader会对请求批量执行，client发送命令会批量发送，通过节省小而多的网络和磁盘操作来提高吞吐量



