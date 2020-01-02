---
uuid: d7ff8720-2d76-11ea-baaa-d5a37c33ff66
title: 性能测试工具之wrk
s: how-to-use-wrk
date: 2020-01-02 23:45:04
tags:
categories:
coauthor: zhaopanhong
---

**wrk简介**

wrk 是一个开源的C语言编写的轻量级HTTP压测工具，支持lua脚本创建复杂测试场景,可以用很少的线程压出很大的并发量。需要注意的是wrk不支持 Windows。

**环境搭建**

mac 下运行：brew install wrk

**参数说明**

使用wrk --help 查看wrk的用法

```
Usage: wrk <options> <url>                            

  Options:                                            

​    -c, --connections <N>  Connections to keep open   

​    -d, --duration    <T>  Duration of test           

​    -t, --threads     <N>  Number of threads to use                                              

​    -s, --script      <S>  Load Lua script file       

​    -H, --header      <H>  Add header to request      

​        --latency          Print latency statistics   

​        --timeout     <T>  Socket/request timeout     

​    -v, --version          Print version details      
```

​                                                  

 -c 表示为保持连接状态的连接数， -d 表示压测时间，单位可以为60s, 1m, 1h, -t 表示执行操作的线程数，-s 表示指定lua脚本执行，  -H, --header 表示为每一个HTTP请求添加HTTP头，--latency 表示压测结束打印延迟统计信息，--timeout   超时时间，默认1s

一般线程数不宜过多，核数的2到4倍足够了，多了反而因为线程切换过多造成效率降低，因为wrk不是使用每个连接一个线程的模型, 而是通过异步网络io提升并发量，所以网络通信不会阻塞线程执行，这也是wrk可以用很少的线程模拟大量网路连接的原因，而现在很多性能工具并没有采用这种方式, 而是采用提高线程数来实现高并发，所以并发量一旦设的很高, 测试机自身压力就很大，测试效果反而下降。

**基础使用**

wrk -t4 -c1000 -d30s -T30s --latency http://www.baidu.com

表示用4个线程来模拟1000个并发连接，测试持续30秒，连接超时30秒，打印出请求的延迟统计信息，输出结果为：

```
Running 30s test @ http://www.baidu.com

  4 threads and 1000 connections

  Thread Stats   Avg      Stdev     Max   +/- Stdev

​    Latency     2.43s     3.78s   27.35s    88.50%

​    Req/Sec    67.94     31.88   191.00     65.84%

  Latency Distribution

​     50%  996.54ms

​     75%    2.57s 

​     90%    6.95s 

​     99%   18.54s 

  7994 requests in 30.09s, 124.47MB read

  Socket errors: connect 0, read 12, write 0, timeout 0

Requests/sec:    265.70

Transfer/sec:      4.14MB
```

结果说明：

4 threads and 1000 connections：总共是4个线程,1000个连接

latency和Req/Sec:代表单个线程的统计数据,latency代表延迟时间,Req/Sec代表单个线程每秒完成的请求数，他们都具有平均值, 标准偏差, 最大值, 正负一个标准差占比。一般我们来说我们主要关注平均值和最大值. 标准差如果太大说明样本本身离散程度比较高. 有可能系统性能波动很大.

Latency Distribution：表示响应时间分布，说明有50%的请求在996.54ms之内,90%在6.95s之内。

 7994 requests in 30.09s, 124.47MB read：在30秒之内总共有7994个请求,总共124.47读取MB的数据

 Socket errors: connect 0, read 12, write 0, timeout 0：总共有12个读错误.

Requests/sec和Transfer/sec：所有线程平均每秒钟完成265.70个请求,每秒钟读取4.14MB数据量

主要关注：

- Socket errors ：socket 错误的数量
- Requests/sec ：每秒请求数量，可以理解为TPS
- Latency： 响应时间分布

**lua脚本的编写**

实际使用中，我们可能不会像上面一样不带任何请求头及请求参数，下面以对一个post请求进行压力测试为例，编写lua脚本如下：

```
wrk.method = "POST"

wrk.headers["Accept"] = "application/json"

wrk.headers["X-Authorization"] = "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE1NzIzMTQwMzksIm5iZiI6MTU3MjMxNDAzOSwianRpIjoiYjRjYjFlYWQtZGZmMy00NzY2LWFhNWItN2ZjOTgyNTA3MWMzIiwiaWRlbnRpdHkiOnsib3JnIjoicm9vbWlzLTIwMTcwMzAxIn0sImZyZXNoIjpmYWxzZSwidHlwZSI6ImFjY2VzcyJ9.CM6_UKkfONH2e3KcSLIzu0eMM_jWvqLZKyanDp_16vY"

wrk.body = "{\"date_ranges\":[{\"start\":\"2019-10-01\",\"end\":\"2019-11-13\"}],\"dimensions\":[{\"name\":\"day\"}],\"metrics\":[{\"name\":\"page_view\"}],\"filters\":{\"org_code\":\"shtvu\"},\"with_empty_buckets\":true}"
```

执行命令：

wrk -d 60s -c 100 -t 4 -s ./page_view.lua "http://xxx/api/v1/analytics/analytics" --latency

表示60s内用4个线程来模拟100个并发连接打印出请求的延迟统计信息，测试结果如下：

```
  4 threads and 100 connections

  Thread Stats   Avg      Stdev     Max   +/- Stdev

​    Latency   438.26ms  240.12ms   1.79s    70.26%

​    Req/Sec    58.13     19.48   130.00     67.91%

  Latency Distribution

​     50%  394.37ms

​     75%  572.89ms

​     90%  763.88ms

​     99%    1.15s 

  13941 requests in 1.00m, 34.65MB read

Requests/sec:    231.97

Transfer/sec:    590.34KB
```

相关链接：https://github.com/wg/wrk