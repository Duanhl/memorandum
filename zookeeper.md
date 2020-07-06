# zookeeper

## 1. overview

## 2. ordering guarantees

1. `Linearizable writes`: all requests that update the state of ZooKeeper are serializable and respect precedence.
2. `FIFO client order`: all requests from a given client are executed in the order that they were sent by the client.

A-linearizability (asynchronous linearizability), allow a client to have multiple outstanding operations;

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



