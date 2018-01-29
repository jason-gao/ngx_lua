# 安装
* OpenResty® 提供官方的预编译包

## ubuntu
* 导入我们的 GPG 密钥
    * wget -qO - https://openresty.org/package/pubkey.gpg | sudo apt-key add -
* 安装 add-apt-repository 命令
    * sudo apt-get -y install software-properties-common
* 添加我们官方 official APT 仓库
    * sudo add-apt-repository -y "deb http://openresty.org/package/ubuntu $(lsb_release -sc) main"
* 更新 APT 索引
    * sudo apt-get update
* sudo apt-get install openresty    
* 这个包同时也推荐安装 openresty-opm 和 openresty-restydoc 包，所以后面两个包会缺省安装上。 如果你不想自动关联安装，可以用下面方法关闭自动关联安装：
  
      sudo apt-get install --no-install-recommends openresty
      
## centos
*   sudo yum install yum-utils
*   sudo yum-config-manager --add-repo https://openresty.org/package/centos/openresty.repo
*   sudo yum install openresty
*   sudo yum install openresty-resty
*   sudo yum --disablerepo="*" --enablerepo="openresty" list available



# 使用 hello world nginx.conf
```
worker_processes  1;
error_log logs/error.log;
events {
    worker_connections 1024;
}
http {
    server {
        listen 8080;
        location / {
            default_type text/html;
            content_by_lua '
                ngx.say("<p>hello, world</p>")
            ';
        }
    }
}
```

* PATH=/usr/local/openresty/nginx/sbin:$PATH
* export PATH
* nginx -p `pwd`/ -c conf/nginx.conf

* curl http://localhost:8080/
## benchmark ab
* ubuntu 
    * apt install apache2-utils
    * ab -c10 -n50000 http://localhost:8080/
```
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient)
Completed 5000 requests
Completed 10000 requests
Completed 15000 requests
Completed 20000 requests
Completed 25000 requests
Completed 30000 requests
Completed 35000 requests
Completed 40000 requests
Completed 45000 requests
Completed 50000 requests
Finished 50000 requests


Server Software:        openresty/1.13.6.1
Server Hostname:        localhost
Server Port:            8080

Document Path:          /
Document Length:        20 bytes

Concurrency Level:      10
Time taken for tests:   1.752 seconds
Complete requests:      50000
Failed requests:        0
Total transferred:      8400000 bytes
HTML transferred:       1000000 bytes
Requests per second:    28545.25 [#/sec] (mean)
Time per request:       0.350 [ms] (mean)
Time per request:       0.035 [ms] (mean, across all concurrent requests)
Transfer rate:          4683.20 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       8
Processing:     0    0   0.2      0       9
Waiting:        0    0   0.2      0       8
Total:          0    0   0.2      0       9

Percentage of the requests served within a certain time (ms)
  50%      0
  66%      0
  75%      0
  80%      0
  90%      0
  95%      1
  98%      1
  99%      1
 100%      9 (longest request)
```      
 
* 重载  openresty -s reload

## 修改server头  header_filter_by_lua

```
server{
        listen 8080;
        location / {
                default_type text/html;
                header_filter_by_lua '
                        ngx.header["server"] = "jasong openresty"
                ';

                content_by_lua '
                        ngx.say("<p>hello, world</p>")
                ';
        }
}

```
      
    