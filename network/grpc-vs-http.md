# gRPC vs HTTP

gRPC和HTTP的对比, 更多的是在携带json的http请求和gRPC请求之间的对比, 因为gRPC本身是基于http2实现的.

1. protobuf相比于json的序列化更优
2. protobuf都是基于http2的, 而大部分http(restful)请求都是基于http1, 这一点性能有较大的差异
3. 客户端代码生成, gRPC的客户端代码生成非常方便, 而http客户端请求的构造需要依赖三方库.
4. 体验上, gRPC的使用就如同调用了本地的方法.
5. 规范上, gRPC严格规定了服务的请求响应格式, 而http请求没有规定.

Ref to: [https://docs.microsoft.com/zh-cn/aspnet/core/grpc/comparison?view=aspnetcore-6.0](https://docs.microsoft.com/zh-cn/aspnet/core/grpc/comparison?view=aspnetcore-6.0)
