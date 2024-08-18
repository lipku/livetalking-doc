
## 3. Usage
分别选择数字人模型、传输方式、tts模型

### 3.1 数字人模型
支持3种模型：ernerf、musetalk、wav2lip，默认用ernerf
#### 3.1.1 ER-Nerf
```
python app.py --model ernerf
```
支持如下参数配置
##### 3.1.1.1 音频特征用hubert
默认用的wav2vec，如果训练模型时用的hubert提取音频特征，用如下命令启动数字人
```
python app.py --asr_model facebook/hubert-large-ls960-ft 
```

##### 3.1.1.2 设置头部背景图片
```
python app.py --bg_img bc.jpg 
```

##### 3.1.1.3 全身视频贴回
-  1.切割训练用的视频
```
ffmpeg -i fullbody.mp4 -vf crop="400:400:100:5" train.mp4 
```
用train.mp4训练模型
- 2.提取全身图片
```
ffmpeg -i fullbody.mp4 -vf fps=25 -qmin 1 -q:v 1 -start_number 0 data/fullbody/img/%d.jpg
```
- 3.启动数字人
```
python app.py --fullbody --fullbody_img data/fullbody/img --fullbody_offset_x 100 --fullbody_offset_y 5 --fullbody_width 580 --fullbody_height 1080 --W 400 --H 400
```
- --fullbody_width、--fullbody_height 全身视频的宽、高
- --W、--H 训练视频的宽、高  
- ernerf训练第三步torso如果训练的不好，在拼接处会有接缝。可以在上面的命令加上--torso_imgs data/xxx/torso_imgs，torso不用模型推理，直接用训练数据集里的torso图片。这种方式可能头颈处会有些人工痕迹。

##### 替换成自己的数字人
替换成自己训练的模型<https://github.com/Fictionarry/ER-NeRF>
```bash
├── data
│   ├── data_kf.json
│   ├── au.csv			
│   ├── pretrained
│   └── └── ngp_kf.pth

```

#### 3.1.2 模型用musetalk
暂不支持rtmp推送
- 安装依赖库
```bash
conda install ffmpeg
pip install --no-cache-dir -U openmim 
mim install mmengine 
mim install "mmcv>=2.0.1" 
mim install "mmdet>=3.1.0" 
mim install "mmpose>=1.1.0"
```
- 下载模型  
下载MuseTalk运行需要的模型，提供一个下载地址<https://caiyun.139.com/m/i?2eAjs2nXXnRgr>  提取码:qdg2
解压后，将models下文件拷到本项目的models下  
下载数字人模型，链接:<https://caiyun.139.com/m/i?2eAjs8optksop>  提取码:3mkt, 解压后将整个文件夹拷到本项目的data/avatars下
- 运行  
python app.py --model musetalk --transport webrtc  
用浏览器打开http://serverip:8010/webrtcapi.html  
可以设置--batch_size 提高显卡利用率，设置--avatar_id 运行不同的数字人
##### 替换成自己的数字人
```bash
git clone https://github.com/TMElyralab/MuseTalk.git
cd MuseTalk
修改configs/inference/realtime.yaml，将preparation改为True
python -m scripts.realtime_inference --inference_config configs/inference/realtime.yaml
运行后将results/avatars下文件拷到本项目的data/avatars下
方法二
执行
cd musetalk 
python simple_musetalk.py --avatar_id 4  --file D:\\ok\\test.mp4
支持视频和图片生成 会自动生成到data的avatars目录下
```

#### 3.1.3 模型用wav2lip
暂不支持rtmp推送
- 下载模型  
下载wav2lip运行需要的模型，链接:<https://pan.baidu.com/s/1yOsQ06-RIDTJd3HFCw4wtA> 密码: ltua
将s3fd.pth拷到本项目wav2lip/face_detection/detection/sfd/s3fd.pth, 将wav2lip.pth拷到本项目的models下  
数字人模型文件 wav2lip_avatar1.tar.gz, 解压后将整个文件夹拷到本项目的data/avatars下
- 运行  
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip_avatar1  
用浏览器打开http://serverip:8010/webrtcapi.html  
可以设置--batch_size 提高显卡利用率，设置--avatar_id 运行不同的数字人
##### 替换成自己的数字人
```bash
cd wav2lip
python genavatar.py --video_path xxx.mp4
运行后将results/avatars下文件拷到本项目的data/avatars下
```

### 3.2 传输模式
支持webrtc、rtcpush、rtmp，默认用rtcpush
#### 3.2.1 webrtc p2p
此种模式不需要srs
```
python app.py --transport webrtc
```
服务端需要开放端口 tcp:8010; udp:50000~60000  
用浏览器打开http://serverip:8010/webrtcapi.html

#### 3.2.2 webrtc推送到srs
- 启动srs
```
export CANDIDATE='<服务器外网ip>'
docker run --rm --env CANDIDATE=$CANDIDATE \
  -p 1935:1935 -p 8080:8080 -p 1985:1985 -p 8000:8000/udp \
  registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5 \
  objs/srs -c conf/rtc.conf
```
- 运行数字人
```python
python app.py --transport rtcpush --push_url 'http://localhost:1985/rtc/v1/whip/?app=live&stream=livestream'
```
用浏览器打开http://serverip:8010/rtcpushapi.html

#### 3.2.3 rtmp推送到srs
- 安装rtmpstream库  
参照<https://github.com/lipku/python_rtmpstream>

- 启动srs
```
docker run --rm -it -p 1935:1935 -p 1985:1985 -p 8080:8080 registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5
```
- 运行数字人
```python
python app.py --transport rtmp --push_url 'rtmp://localhost/live/livestream'
```
用浏览器打开http://serverip:8010/echoapi.html

### 3.3 TTS模型
支持edgetts、gpt-sovits、xtts，默认用edgetts
#### 3.3.1 gpt-sovits
服务部署参照[gpt-sovits](/tts/README.md)  
运行
```
python app.py --tts gpt-sovits --TTS_SERVER http://127.0.0.1:9880 --REF_FILE data/ref.wav --REF_TEXT xxx
```
REF_TEXT为REF_FILE中语音内容，时长不宜过长

#### 3.3.2 xtts
运行xtts服务，参照<https://github.com/coqui-ai/xtts-streaming-server>
```
docker run --gpus=all -e COQUI_TOS_AGREED=1 --rm -p 9000:80 ghcr.io/coqui-ai/xtts-streaming-server:latest
```
然后运行，其中ref.wav为需要克隆的声音文件
```
python app.py --tts xtts --REF_FILE data/ref.wav --TTS_SERVER http://localhost:9000
```

### 3.4 视频编排
- 1，生成素材
```
ffmpeg -i xxx.mp4 -s 576x768 -vf fps=25 -qmin 1 -q:v 1 -start_number 0 data/customvideo/image/%08d.png
ffmpeg -i xxx.mp4 -vn -acodec pcm_s16le -ac 1 -ar 16000 data/customvideo/audio.wav
```
其中-s与输出视频大小一致
- 2，编辑data/custom_config.json  
指定imgpath和audiopath。  
设置audiotype，说明：0表示推理视频，不用设置；1表示静音视频，如果不设置默认用推理视频代替; 2以上自定义配置
- 3，运行
```
python app.py --transport webrtc --customvideo_config data/custom_config.json
```
- 4，打开http://<serverip>:8010/webrtcapi-custom.html  
填写custom_config.json中配置的audiotype，点击切换视频

### 3.5 使用LLM模型进行数字人对话

目前借鉴数字人对话系统[LinlyTalker](https://github.com/Kedreamix/Linly-Talker)的方式，LLM模型支持Chatgpt,Qwen和GeminiPro。需要在app.py中填入自己的api_key。    

用浏览器打开http://serverip:8010/rtcpushchat.html


### 3.6 更多功能集成
- 语音输入、知识库问答 [Fay](https://github.com/xszyou/Fay)
- 虚拟主播，字幕抓取 [Luna](https://github.com/Ikaros-521/AI-Vtuber)


