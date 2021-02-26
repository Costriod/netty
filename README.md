当前分支主要是研究netty-4.1.58.Final版本server端从启动到端口监听整个过程，从ServerBootstrap开始一步步看源码以及给源码标上注释，熟悉netty底层处理逻辑

```java
ServerBootstrap bootstrap = new ServerBootstrap();
NioEventLoopGroup boss = new NioEventLoopGroup();//用于处理channel连接
NioEventLoopGroup worker = new NioEventLoopGroup(8);//用于处理channel的IO操作
try {
    bootstrap.group(boss, worker)
    .channel(NioServerSocketChannel.class)
    .option(ChannelOption.SO_BACKLOG, 512)//BACKLOG是TCP established的连接数上限
    .handler(new LoggingHandler(LogLevel.DEBUG))
    .childHandler(new ChannelInitializer<Channel>() {
        @Override
        protected void initChannel(Channel ch) throws Exception {
            ChannelPipeline p = ch.pipeline();
            p.addLast(new CustomDecoder());
            p.addLast(new CustomEncoder());
            p.addLast(new CustomHandler());
        }
    });
    ChannelFuture sync = bootstrap.bind(8000).sync();
    sync.channel().closeFuture().sync();
} catch (InterruptedException e) {
    
} finally {
    worker.shutdownGracefully();
    boss.shutdownGracefully();
}
```