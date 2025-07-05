## 2. Quick Start

1. 设置阿里云访问密钥,访问千问模型用
```bash
export DASHSCOPE_API_KEY=xxx
```

2. 运行命令 
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1
```
3. 安装并运行客户端，设置服务端地址，连接视频。与数字人asr语音交互

---
说唤醒词’你好‘触发数字人，然后提问，数字人回答问题。   
回答过程中可以通过唤醒词’你好‘打断提问，说其他词不影响数字人    
数字人回答完后可以继续提问。  

