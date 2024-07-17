# Nginx 状态

### 说明

参考文档：[https://nginx.org/en/docs/http/ngx\_http\_stub\_status\_module.html](https://nginx.org/en/docs/http/ngx\_http\_stub\_status\_module.html)

ngx\_http\_stub\_status\_module 显示的状态为整个 Nginx 的全局状态，而非仅仅某一个 server 的状态



### status.conf

```nginx
server {
    listen 80;
    server_name test.yunwei361.com;

    location = /nginx_status {
        stub_status;
    }
}
```



### 压力测试

```bash
ab -n 1000 -c 3 http://test.yunwei361.com/
This is ApacheBench, Version 2.3 <$Revision: 1430300 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking test.yunwei361.com (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        nginx/1.24.0
Server Hostname:        test.yunwei361.com
Server Port:            80

Document Path:          /
Document Length:        615 bytes

Concurrency Level:      3
Time taken for tests:   47.192 seconds
Complete requests:      1000
Failed requests:        0
Write errors:           0
Total transferred:      848000 bytes
HTML transferred:       615000 bytes
Requests per second:    21.19 [#/sec] (mean)
Time per request:       141.576 [ms] (mean)
Time per request:       47.192 [ms] (mean, across all concurrent requests)
Transfer rate:          17.55 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       65   70   1.9     71      77
Processing:    65   71   1.9     71      77
Waiting:       65   71   1.9     71      77
Total:        131  141   3.8    142     154

Percentage of the requests served within a certain time (ms)
  50%    142
  66%    144
  75%    144
  80%    144
  90%    145
  95%    146
  98%    147
  99%    150
 100%    154 (longest request)
```



### 访问

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

### 名词解释

```
Active connections:
The current number of active client connections including Waiting connections.

accepts:
The total number of accepted client connections.

handled:
The total number of handled connections. Generally, the parameter value is the same as accepts unless some resource limits have been reached (for example, the worker_connections limit).

requests:
The total number of client requests.

Reading:
The current number of connections where nginx is reading the request header.

Writing:
The current number of connections where nginx is writing the response back to the client.

Waiting:
The current number of idle client connections waiting for a request.
```
