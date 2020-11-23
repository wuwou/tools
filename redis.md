- Redis安装方法：
  
  ```cpp
  linux下：
  # wget http://download.redis.io/releases/redis-6.0.8.tar.gz
  # tar xzf redis-6.0.8.tar.gz
  # cd redis-6.0.8
  # make
  # cd src
  # ./redis-server     
  ```

这时候就启动了redis服务

![](https://github.com/wuwou/tools/blob/master/image/1123_1.PNG)

再打开另一个窗口，原来的不要关闭，不然就无法访问服务端了。

```cpp
# cd src
# ./redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```

启动redis后服务进程后，就可以使用测试客户端服务程序redis-cli和redis服务交互了。

![](https://github.com/wuwou/tools/blob/master/image/1123_2.PNG)






















