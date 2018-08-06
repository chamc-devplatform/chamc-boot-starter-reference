# 其他配置

1. `chamc.service.feign.log-level`：在消费方设置，用于指定消费方调用提供方服务时打印日志的级别，包括none、headers、basic、full四个级别。默认为none。

   full级别日志打印示例：

   ```text
    [Client1RemoteService2#create] ---> POST http://provider/create HTTP/1.1       
    [Client1RemoteService2#create] Content-Type: application/json;charset=UTF-8
    [Client1RemoteService2#create] Content-Length: 86
    [Client1RemoteService2#create]
    [Client1RemoteService2#create] {"name":"abc","age":12,"birthday":null,"address":{"street":null,"no":123,"city":"BJ"}}
    [Client1RemoteService2#create] ---> END HTTP (86-byte body)
    ......// 以上是发送请求时的日志，以下是接收到返回结果的日志
    [Client1RemoteService2#create] <--- HTTP/1.1 200 (660ms)
    [Client1RemoteService2#create] content-type: application/json;charset=UTF-8
    [Client1RemoteService2#create] date: Fri, 02 Mar 2018 07:05:40 GMT
    [Client1RemoteService2#create] transfer-encoding: chunked
    [Client1RemoteService2#create] x-application-context: provider:8899
    [Client1RemoteService2#create] 
    [Client1RemoteService2#create] {"id":null,"name":"abc","age":12,"birthday":null,"address":{"street":null,"no":123}}
    [Client1RemoteService2#create] <--- END HTTP (84-byte body)
   ```

## 4 “How-to”指南

## 5 附录

