
## 3. Usage

### 3.1 高清wav2lip模型
- 下载模型  
将高清模型拷到代码models目录下    
下载hubert模型放到models目录下
```python
import huggingface_hub
from huggingface_hub import snapshot_download
snapshot_download('facebook/hubert-large-ls960-ft', local_dir='models/hubert-large-ls960-ft')
```
- 运行
1. 192模型  
```
python app.py --transport webrtc --model wav2lipls --avatar_id wav2lipls_avatar1
```   
 替换成自己的数字人
```bash
cd wav2lip
python genavatar.py  --video_path xxx.mp4  --img_size 192 --avatar_id wav2lipls_avatar1
运行后将results/avatars下文件拷到本项目的data/avatars下
```
2. 384模型  
修改wav2lipls/models/human.py，将face_size = 192改为face_size = 384
```  
python app.py --transport webrtc --model wav2lipls --avatar_id wav2lipls384_avatar1 --modelres 384
```  
替换成自己的数字人
```bash
cd wav2lip
python genavatar.py  --video_path xxx.mp4  --img_size 384 --avatar_id wav2lipls384_avatar1
运行后将results/avatars下文件拷到本项目的data/avatars下
```
3. 使用微调模型
将微调后模型拷贝到models目录下  
运行命令时添加参数 --modelfile xxx，如
```
python app.py --transport webrtc --model wav2lipls --avatar_id wav2lipls_finetune --modelfile checkpoint_step001500000_finetune.pth
```

### 3.2 不限时长avatar形象
- 运行
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --imgcache_num 400
```
其中imgcache_num为图片缓存张数，一般设置为400以上。设置太低会导致加载太频繁



### 3.3 动作编排
#### 3.3.1 自定义不说话时动作(audiotype为1)
- 1，切割素材
录制一段无动作和有动作衔接的视频，用剪映等软件找到衔接的时间点，用ffmepg命令切割成无动作和有动作视频。以网上的‘韵巧.mp4’为例，
```
ffmpeg -i input.mp4 -ss 00:00:00 -to 00:00:02 -c copy -copyts -avoid_negative_ts make_zero silence.mp4
ffmpeg -i input.mp4 -ss 00:00:02 -to 00:00:20 -c copy -copyts -avoid_negative_ts make_zero speak.mp4
```
- 2，用无动作视频生成静音素材
```
ffmpeg -i silence.mp4 -vf fps=25 -qmin 1 -q:v 1 -start_number 0 data/customvideo/image/%08d.png
```
- 3，编辑data/custom_config.json  
指定imgpath,设置audiotype为1
```json
[
   {
        "audiotype":1, 
        "imgpath":"data/customvideo/image"
    }
]
```
- 4，用有动作视频生成avatar，如
```
cd wav2lip
python genavatar.py  --video_path speak.mp4  --img_size 192 --avatar_id wav2lipls_avatar1
```
其中img_size根据不同的模型分别设置

- 5，运行  
高清模型
```
python app.py --transport webrtc --model wav2lipls --avatar_id wav2lipls_avatar1 --customvideo_config data/custom_config.json
```
普通模型
```
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --customvideo_config data/custom_config.json
```
#### 3.3.2 其他动作  
唤醒时动作(audiotype为2)、思考时动作(audiotype为3) 
用切割后视频制作动作素材 
```
ffmpeg -i xxx.mp4 -vf fps=25 -qmin 1 -q:v 1 -start_number 0 data/customvideo/reply/%08d.png
ffmpeg -i xxx.mp4 -vn -acodec pcm_s16le -ac 1 -ar 16000 data/customvideo/reply.wav
```
编辑data/custom_config.json  
```json
[
    {
        "audiotype":1, 
        "imgpath":"data/customvideo/image"
    },
    {
        "audiotype":2, 
        "imgpath":"data/customvideo/reply",
        "audiopath":"data/customvideo/reply.wav"
    },
    {
        "audiotype":3, 
        "imgpath":"data/customvideo/think",
        "audiopath":"data/customvideo/think.wav"
    }
]
```
运行命令同上


### 3.4 实时音频流输入
通过websocket连接ws://serverip:8010/ws  
连接后发送{'cmd':'login','sessionid':sessionid},设置当前连接的sessionid，其中sessionid是从offer接口返回的  
如果音频数据流为pcm格式，发送{'cmd':'setpcm','samplerate':xxx}设置音频采样率，其中xxx设置为pcm数据的真实采样率  
然后每段音频数据，通过websocket直接发送二进制数据如ws.send(arrayBuffer)  
代码参照webrtcapi-avatar.html