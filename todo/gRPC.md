[TOC]

## rpc和http的区别

1. 首先http是指从客户端到服务器端的请求消息，rpc是远程过程调用协议,是一种计算机通信协议



## gRPC （Remote Procedure Call ）

1. [gRPC](http://www.oschina.net/p/grpc-framework)  是一个高性能、开源和通用的 RPC 框架，面向移动和 HTTP/2 设计。目前提供 C、Java 和 Go 语言版本，分别是：grpc, grpc-java, grpc-go. 其中 C 版本支持 C, C++, Node.js, Python, Ruby, Objective-C, PHP 和 C# 支持. [中文官网]( http://doc.oschina.net/grpc )

![](https://upload-images.jianshu.io/upload_images/1456103-d058dcea9171755f.png?imageMogr2/auto-orient/strip|imageView2/2/w/552/format/webp)

2. gRPC 提供 protocol   编译插件，能够从一个服务定义的 .proto 文件生成客户端和服务端代码。通常 gRPC 用户可以在服务端实现这些API，并从客户端调用它们。 





## 1.proto文件

字段类型
	double——>java：double
	float——>java：	float
	int32——>java ：int	[使用可变长度编码。编码负数的效率低 - 如果你的字段可能有负值，请改用 sint32]
	int64——>java：	long [使用可变长度编码。编码负数的效率低 - 如果你的字段可能有负值，请改用 sint64]
	uint32——> java：int  [使用可变长度编码]
	uint64——> java ：int/long [使用可变长度编码]
	sint32——> java：int	[使用可变长度编码。有符号的 int 值。这些比常规 int32 对负数能更有效地编码]
	sint64——> java：long  [使用可变长度编码。有符号的 int 值。这些比常规 int64 对负数能更有效地编码]
	fixed32——>java：int [总是四个字节。如果值通常大于 228，则比 uint32 更有效。]
	fixed64——>java：long  [总是八个字节。如果值通常大于 256，则比 uint64 更有效]
	sfixed32	——>java : int [总是四个字节]
	sfixed64——>java：long [总是八个字节	]
	bool	 ——>java :boolean
	string ——>java：String [字符串必须始终包含 UTF-8 编码或 7 位 ASCII 文本]
	bytes——>java：ByteString [可以包含任意字节序列	]

- 其他类型 
  
  1.列表 - list

~~~java
reapeat string  str = 1；
~~~

​	   2. map 

~~~java
map<string,string>  userMap = 1；
~~~

​	   3. 	实体类型 --message

~~~java

message User{   

         string  name = 1；

         string  age = 2；

}

~~~

​	    4. 泛型(泛型最终会对应的实体类型也要先在.proto中定义好 )  Any

~~~java
/*  需要提前导入Any文件*/       
import "google/protobuf/any.proto";     


message User{   

         string  name = 1；

         string  age = 2；

		google.protobuf.Any data = 3;
}
 

~~~

## 2.具体使用

#### 	1.maven 依赖

~~~xml
	   
	   <dependency>
            <groupId>com.google.protobuf</groupId>
            <artifactId>protobuf-java</artifactId>
            <version>3.4.0</version>
        </dependency>
        <dependency>
            <groupId>io.grpc</groupId>
            <artifactId>grpc-all</artifactId>
            <version>1.12.0</version>
        </dependency>
~~~

#### 	2. 插件 

通过该插件生成对应的java代码

~~~xml
<plugin>
    <groupId>org.xolstice.maven.plugins</groupId>
    <artifactId>protobuf-maven-plugin</artifactId>
    <version>0.5.0</version>
    <configuration>
        <protocArtifact>
            com.google.protobuf:protoc:3.4.0:exe:${os.detected.classifier}
        </protocArtifact>
        <pluginId>grpc-java</pluginId>
        <pluginArtifact>io.grpc:protoc-gen-grpc-java:${grpc.version}:exe:${os.detected.classifier}</pluginArtifact>
        <!-- proto 文件路径 -->
        <protoSourceRoot>${project.basedir}/src/main/resources/static/proto</protoSourceRoot>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>compile</goal>
                <goal>compile-custom</goal>
            </goals>
        </execution>
    </executions>
</plugin>
~~~

#### 	3.proto文件

自定义proto文件，通过插件生成相关的java代码

~~~protobuf
syntax = "proto3";

import "google/protobuf/any.proto";

option java_multiple_files = true;
//生成的java
option java_package = "com.reagent.service";
option java_outer_classname = "MessageModel";


//生产带有抽象方法的java类
service SendMessage{
     rpc sendMessageWithResult(Msg) returns (RequestResult){}

     rpc sendMessage(Msg) returns (RequestResult){}

}

message Msg {
     string topic = 1;
     string tag = 2;
     string msgbody = 3;
}

message RequestResult {
     string resultCode = 1;
     string resultMsg = 2;
     google.protobuf.Any data = 3;
     enum ResultCode{
          RESULT_CODE_SUCCESS = 0;
          RESULT_CODE_FAIL = 1;
     }
}

message Data{
     string msg = 1;
}


~~~

#### 	common(通过插件生成的java类和gRPC接口)

​		1. 实体java类

​		2. XXXGRpc

#### 	gRPC服务端

​		1. 实现类
​			通过继承XXXGRpc文件 的XXXImplBase抽象方法重写方法的实现类

~~~java
@Override
public void sendMessageWithResult(Msg request, StreamObserver<RequestResult> responseObserver){
    com.reagent.common.util.RequestResult result = producer
        .sendMsgWithResult(
        	request.getTopic(), 
        	request.getTag(), 
        	request.getMsgbody());
    //        Data data = Data.newBuilder().setMsg("pretend to return result.getData()").build();
    //封装返回的数据
    RequestResult requestResult = RequestResult.newBuilder()
        //.setData(Any.pack(data))
        .setResultMsg(result.getResultMsg())
        .setResultCode(result.getResultCode()+"")
        .build();

    responseObserver.onNext(requestResult);
    responseObserver.onCompleted();
}
~~~



​		2.服务端

​			启动服务端，配置端口......

~~~java
private Server server;

private void start() throws IOException {
    server = ServerBuilder.forPort(port).addService(service).build();
    server.start();
    logger.info("Server has started, listening on " + port);
    Runtime.getRuntime().addShutdownHook(new Thread() {
        @Override
        public void run() {

            GRpcServer.this.stop();

        }
    });
}

private void blockUntilShutdown() throws InterruptedException {
    if (server != null) {
        server.awaitTermination();
    }
}
~~~



#### 	gRPC客户端

​		1. 客户端
​			配置服务端地址和端口
​			编写请求方法(接口)   通过builder封装实体，再通过XXXGRpc文件中的XXXBlockingStub来发送请求



~~~java
//    @Value("defaultHost")
//    private String host = ;

//    @Value("defaultPort")
//    private int port;
private SendMessageGrpc.SendMessageBlockingStub sendMessageBlockingStub;

public GRpcClient(){
    this(DEFAULT_HOST,DEFAULT_PORT);
}

public GRpcClient(String host, int port) {

    this(ManagedChannelBuilder.forAddress(host,port).usePlaintext(true).build());

}


public GRpcClient(ManagedChannel managedChannel) {
    this.managedChannel = managedChannel;
    this.sendMessageBlockingStub = SendMessageGrpc.newBlockingStub(managedChannel);
}

public RequestResult sendMsgWithResult(String topic, String tag, String messageBody){
    Msg msg = Msg.newBuilder()
        .setTopic(topic)
        .setTag(tag)
        .setMsgbody(messageBody)
        .build();
    RequestResult result = sendMessageBlockingStub
        						.sendMessageWithResult(msg);
    logger.warn("result: { resultCode: "
                +result.getResultCode()
                +", resultMsg: "+result.getResultMsg()
                +", resultData: "+result.getData()+"}");
    return result;
}
~~~

