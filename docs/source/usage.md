
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
python genavatar_yolo.py  --video_path xxx.mp4  --img_size 192 --avatar_id wav2lipls_avatar1
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
python genavatar_yolo.py  --video_path xxx.mp4  --img_size 384 --avatar_id wav2lipls384_avatar1
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

### 3.4 avatar实时切换
1. 准备同一个人物多个不同动作的视频，多个视频保持分辨率一致，背景一致。
2. 用上述视频生成多个avatar形象，放置在data/avatars下
3. 运行服务   
通过json字符串描述要加载的多个avatar供切换用，如[{'avatarid':'avatar1'},{'avatarid':'avatar2'}]  
运行示例，主avatar不要在multiavatar_config中描述
```
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_silence  --multiavatar_config [{\"avatarid\":\"wav2lip256_action\"}]
```
4. 客户端控制切换avatar，在api接口/human中设置avatarid字段，如‘avatarid’:‘wav2lip256_action’，具体查看api接口文档  

### 3.5 同一个画面多个数字人
1. 准备原始视频，视频中有多个人脸，不要相互遮挡；人物相对位置固定，比如A在B左边就一直在左边，不要移动到B的右边。
2. 用原始视频生成多个avatar
```bash
cd wav2lip
python genavatar_yolo_multi.py  --video_path ~/multiclip.mp4  --img_size 256 --avatar_id wav2lip_left --nth_avatar 0
python genavatar_yolo_multi.py  --video_path ~/multiclip.mp4  --img_size 256 --avatar_id wav2lip_right --nth_avatar 1
```
nth_avatar: specifies which face to use for lip-syncing. 0 means the most left face, 1 means the second left face, and so on  
3. 运行服务
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip_left  --multiavatar_config [{\"avatarid\":\"wav2lip_right\"}] --multiavatar_use_sameframe
```
4. 客户端控制切换avatar和对应音色，在api接口/human中设置avatarid和tts字段，具体查看api接口文档 

### 3.6 实时音频流输入
通过websocket连接ws://serverip:8010/ws  
连接后发送{'cmd':'login','sessionid':sessionid},设置当前连接的sessionid，其中sessionid是从offer接口返回的  
- 如果音频数据流为pcm格式，发送{'cmd':'setpcm','samplerate':xxx}设置音频采样率，其中xxx设置为pcm数据的真实采样率  
- 切换avatar时,发送{'cmd':'setavatar','avatarid':'xxx'}，其中xxx为multiavatar_config配置的avatarid  

然后每段音频数据，通过websocket直接发送二进制数据如ws.send(arrayBuffer)  
代码参照webrtcapi-avatar.html

### 3.7 摄像头实时驱动数字人形象动作和表情
1. 运行服务
```bash
python app.py --transport webrtc --model wav2lip --liveavatar 0
```
其中0表示服务端本地第几个摄像头  
2. 主播必须整个正脸在摄像头取景范围内，不用说话，可以做动作和表情  
3. 客户端通过api接口/human控制说话内容  
如果服务端没有摄像头，可以通过视频文件测试功能
```bash
python app.py --transport webrtc --model wav2lip --liveavatar xxx.mp4
```