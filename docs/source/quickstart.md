## 2. Quick Start
默认采用ernerf模型，webrtc推流到srs  
### 2.1 运行srs
```bash
export CANDIDATE='<服务器外网ip>' #如果srs与浏览器访问在同一层级内网，不需要执行这步
docker run --rm --env CANDIDATE=$CANDIDATE \
  -p 1935:1935 -p 8080:8080 -p 1985:1985 -p 8000:8000/udp \
  registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5 \
  objs/srs -c conf/rtc.conf
```
备注：<font color=red>服务端需要开放端口 tcp:8000,8010,1985; udp:8000</font>

### 2.2 启动数字人：

```python
python app.py
```

如果访问不了huggingface，在运行前
```
export HF_ENDPOINT=https://hf-mirror.com
```

用浏览器打开http://serverip:8010/rtcpushapi.html, 在文本框输入任意文字，提交。数字人播报该段文字  
