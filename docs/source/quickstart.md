## 2. Quick Start
- 下载模型  
下载wav2lip运行需要的模型，链接:<https://pan.baidu.com/s/1yOsQ06-RIDTJd3HFCw4wtA> 密码: ltua  
将wav2lip384.pth拷到本项目的models下, 重命名为wav2lip.pth;  
将wav2lip384_avatar1.tar.gz解压后整个文件夹拷到本项目的data/avatars下
- 运行  
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip384_avatar1  
用浏览器打开http://serverip:8010/webrtcapi.html , 在文本框输入任意文字，提交。数字人播报该段文字

<font color=red>服务端需要开放端口 tcp:8010; udp:1-65536 </font>

如果访问不了huggingface，在运行前
```
export HF_ENDPOINT=https://hf-mirror.com
```

