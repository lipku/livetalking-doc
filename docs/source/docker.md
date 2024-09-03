## 4. Docker Run  
不需要前面的安装，直接运行。
```
docker run --gpus all -it --network=host --rm registry.cn-beijing.aliyuncs.com/codewithgpu2/lipku-metahuman-stream:vjo1Y6NJ3N
```
代码在/root/metahuman-stream，先git pull拉一下最新代码，然后执行命令同usage

提供如下镜像
- autodl镜像：<https://www.codewithgpu.com/i/lipku/metahuman-stream/base>  
[autodl教程](autodl/README.md)

- 好易智算镜像(不需要单独部srs服务器)，<https://bbs.haoee.com/postDetail/657>  
在后台运行如下命令
```
nginx
cd ~/srs/trunk
./objs/srs -c conf/rtc.tcp.udp.conf
cd ~/metahuman-stream
python app.py 
```
浏览器打开http:<haoee公网访问地址>/rtcpushapi.html，如http://www.haoee.com:25232/rtcpushapi.html