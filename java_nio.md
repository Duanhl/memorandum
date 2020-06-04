
# JAVA NIO

## api介绍

### Buffer

<pre>

public abstract class Buffer {

    // Sets this buffer's mark at its position.
    public final Buffer mark();

    //Resets this buffer's position to the previously-marked position.
    public final Buffer reset();

    //Clears this buffer.  The position is set to zero, the limit is
    // set to the capacity, and the mark is discarded.
    public final Buffer clear();

    //Flips this buffer.  The limit is set to the current position 
    //and then the position is set to zero.  If the mark is defined 
    //then it is discarded.
    public final Buffer flip();

    //Rewinds this buffer.  The position is set to zero and the mark
    //is discarded.
    public final Buffer rewind();

    //Tells whether there are any elements between the current 
    //position and the limit.
    public final boolean hasRemaining()

}
</pre>

buffer类是nio中的基石，代表一个可以重复使用的缓冲区，这个缓冲区组织成一个数组的形状，有三个关键指标：

* position  //当前操作位置
* limit     //操作限制位置
* capactity //底层数组容量

![nio buffer](./img/buffers-modes.png)

当你想从读模式切换成写模式时，调用`clear()`方法，`position`设为0，`limit`设为`capactity`，整个buffer都是可以写入数据时。想要读取写入的数据，即从写模式切换成读模式，调用`flip`方法，`limit`设为当前`position`，然后`position`设为0,可以读取到最多直到刚才写入的位置的数据。

当你想重复读取数据，比如先把buffer的数据写入到channel中，然后又想把buffer的数据读入到一个数组中，可以调用`rewinds`，这个方法仅把`position`设为0,不会改变`limit`，可以重复进行读取或者写入。

`mark()`和`reset()`方法成对，用`mark()`做好标记，用`reset()`把`position`重置到`mark`锁所标记的地方，允许对某个片段重复操作。

Buffer的子类有

* ByteBuffer
* CharBuffer
* DoubleBuffer
* FloatBuffer
* IntBuffer
* LongBuffer
* ShortBuffer

这些子类分别代表某种基本类型的Buffer。这些类的实现，以ByteBuffer为例，有两种方式：

* HeapByteBuffer
* MappedByteBuffer
* DirectByteBuffer

第一种方式，`HeapByteBuffer`，和普通的java代码一样，用`new Byte[cap]`创建一个byte数组，在上面进行操作，没什么可说的。着重说下后面两种，`DirectByteBuffer`是直接用`unsafe`类申请一块堆外内存作为Buffer的底层数组，`MappedByteBuffer`则更进一步，做了一层内存映射文件(mmap)，把堆外内存数组映射到文件上，对数组的操作可以同步映射到内核空间上，并且由操作系统同步到文件上。按道理说`MappedByteBuffer`应该作为`DirectByteBuffer`的子类，但是实现上反过来了，`MappedByteBuffer`可以用`FileChannel.map()`方法创建。

### Channel

channel是nio包中对于管道的抽象，和stream有很多相似点，不同点主要集中在：

* channel的数据流通是双向的，可以读取，也可以写入
* channel能够异步的读取和写入
* channel读取，写入的作用对象永远只能是`Buffer`

Channel的重要实现有四种：

* FileChannel
* DatagramChannel
* SocketChannel
* ServerSocketChannel

`FileChannel`可以用`RandomAccessFile#getChannel()`来打开，以随机访问文件的方式打开，可以随机进行读写，利用`FileChannel.map()`，可以对这个文件做内存映射。

`DatagramChannel`是对UDP连接的管道抽象。`SocketChannel`是对客户端TCP/IP连接的管道抽象。`ServerSocketChannel`是对服务端TCP/IP连接的管道抽象。这三个类继承自`SelectableChannel`，意味着这三种管道能够进行IO多路复用操作，这也是nio包的核心功能。

能够进行读写的Channel,都有下列方法:

<pre>
    read(Buffer);
    read(Buffer[]);
    write(Buffer);
    write(Buffer[]);
</pre>

channel可以通过`read`方法，把数据从`channel`中写入到`buffer`，也可以通过`write`方法，把`buffer`的数据写入到`channel`中，`buffer`的数量可以是一个，也可以是多个。

`FileChannel`还有两个特殊的api，

<pre>
  transferFrom(ReadableByteChannel, long, long);
  transferTo(long, long, WritableByteChannel);
</pre>

可以直接写入或者读取到其它channel中