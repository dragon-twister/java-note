## Netty的零拷贝

在操作系统层面上的零拷贝是指避免在用户态与内核态之间来回拷贝数据的技术。

Netty中的零拷贝与操作系统层面上的零拷贝不太一样, Netty的零拷贝完全是在用户态(Java层面)的，更多是数据操作的优化。

Netty的零拷贝主要体现在五个方面

1. Netty的接收和发送ByteBuffer使用直接内存进行Socket读写，不需要进行字节缓冲区的二次拷贝。如果使用JVM的堆内存进行Socket读写，JVM会将堆内存Buffer拷贝一份到直接内存中，然后才写入Socket中。相比于使用直接内存，消息在发送过程中多了一次缓冲区的内存拷贝。

2. Netty的文件传输调用FileRegion包装的transferTo方法，可以直接将文件缓冲区的数据发送到目标Channel，避免通过循环write方式导致的内存拷贝问题。

   ```text
   @Override
   public void channelRead0(ChannelHandlerContext ctx, String msg) throws Exception {
    RandomAccessFile raf = null;
    long length = -1;
    try {
    // 1. 通过 RandomAccessFile 打开一个文件.
    raf = new RandomAccessFile(msg, "r");
    length = raf.length();
    } catch (Exception e) {
    ctx.writeAndFlush("ERR: " + e.getClass().getSimpleName() + ": " + e.getMessage() + '\n');
    return;
    } finally {
    if (length < 0 && raf != null) {
    raf.close();
    }
    }
    ctx.write("OK: " + raf.length() + '\n');
    if (ctx.pipeline().get(SslHandler.class) == null) {
    // SSL not enabled - can use zero-copy file transfer.
    // 2. 调用 raf.getChannel() 获取一个 FileChannel.
    // 3. 将 FileChannel 封装成一个 DefaultFileRegion
    ctx.write(new DefaultFileRegion(raf.getChannel(), 0, length));
    } else {
    // SSL enabled - cannot use zero-copy file transfer.
    ctx.write(new ChunkedFile(raf));
    }
    ctx.writeAndFlush("\n");
   }
   ```

3. Netty提供CompositeByteBuf类, 可以将多个ByteBuf合并为一个逻辑上的ByteBuf, 避免了各个ByteBuf之间的拷贝。

   ```text
   ByteBuf header = ...
   ByteBuf body = ...
   // 新建CompositeByteBuf对象
   CompositeByteBuf compositeByteBuf = Unpooled.compositeBuffer();
   // 第一个参数是true, 表示当添加新的ByteBuf时, 自动递增 CompositeByteBuf 的 writeIndex。如果不传第一个参数或第一个参数为false，则合并后的compositeByteBuf的writeIndex不移动，即不能从compositeByteBuf中读取到新合并的数据。
   compositeByteBuf.addComponents(true,header,body);
   ```

4. 通过ByteBuf Unpooled.wrappedBuffer(byte[] array)操作, 我们可以将byte[]数组包装成一个Netty ByteBuf对象, 进而避免拷贝操作。

   ```text
   byte[] bytes = ...
   ByteBuf byteBuf = Unpooled.wrappedBuffer(bytes);
   ```

5. ByteBuf支持slice操作，可以将ByteBuf分解为多个共享同一个存储区域的ByteBuf, 避免内存的拷贝。

   ```text
   ByteBuf byteBuf = ...
   ByteBuf header = byteBuf.slice(0, 5);
   ByteBuf body = byteBuf.slice(5, 10);
   ```


