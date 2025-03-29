## 2. Quick Start

1. 设置腾讯云和阿里云访问密钥
阿里云访问千问模型，腾讯云访问语音识别服务
```bash
export TENCENT_APPID=xxx
export TENCENT_SECRET_KEY=xxx
export TENCENT_SECRET_ID=xxx
export DASHSCOPE_API_KEY=xxx
```
2. 将数字人应答音频放到data/reply.mp3，数字人离开时音频放到data/leave.mp3 （此步可以不做，无提示音播放）
3. 生成avatar
```bash
cd wav2lip
python genavatar.py  --video_path xxx.mp4  --img_size 192 --avatar_id wav2lipls_avatar1
```
将生成的wav2lipls_avatar1拷到data/avatars下  

4. 运行命令 
```bash
python app.py --transport webrtc --model wav2lipls --avatar_id wav2lipls_avatar1 --asrtype tencent --max_session 10
```
5. 浏览器打开页面http://serverip:8010/dashboard-pro.html  
因为需要采集音频，需要在浏览器加白名单。在浏览器地址框输入edge://flags/#unsafely-treat-insecure-origin-as-secure，将服务端网址输入下面框中并重启浏览器
![img.png](./assets/audio-input-browser.jpg)  

---
说唤醒词’你好‘触发数字人，然后提问，数字人回答问题。   
回答过程中可以通过唤醒词’你好‘打断。  
数字人回答完后可以继续提问。  
一段时间没有提问后需要再次唤醒才能提问。   
运行命令时可配置参数  
```
--hotword 默认 '你好'
--session_time  默认30秒
```
