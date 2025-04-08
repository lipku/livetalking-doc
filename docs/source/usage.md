
## 3. Usage

### 3.1 高清wav2lip模型
- 下载模型  
将高清模型拷到代码models目录下    
下载hubert模型放到models目录下
```python
import huggingface_hub
from huggingface_hub import snapshot_download
huggingface_hub.login(token) # token 从 https://huggingface.co/settings/tokens 获取
snapshot_download('facebook/hubert-large-ls960-ft', local_dir='models/hubert-large-ls960-ft')
```
- 运行  
python app.py --transport webrtc --model wav2lipls --avatar_id wav2lipls_avatar1 --asrtype tencent --max_session 10  
用浏览器打开http://serverip:8010/dashboard-pro.html  
可以设置--batch_size 提高显卡利用率，设置--avatar_id 运行不同的数字人
##### 替换成自己的数字人
```bash
cd wav2lip
python genavatar.py  --video_path xxx.mp4  --img_size 192 --avatar_id wav2lipls_avatar1
运行后将results/avatars下文件拷到本项目的data/avatars下
```

### 3.2 课程播放
将课程内容分割成每句话，并用tts转成语音文件。参照data/lesson.json描述课程
```json
[
    {
        "text":"希望你过得比我还好呦",  //该句话内容
        "audiopath":"data/lesson/zero_shot_prompt.wav",  //该句话音频文件，单声道 16KHz
        "action": {"image":"http://192.168.1.3/image.jpg"}  //格式自定义，数字人播放该句话时会把这部分内容传到前端，由前端解析做相应动作，比如打开对应图片或者网址等
    },
    {
        "text":"test2", 
        "audiopath":"data/lesson/zhongjie.wav", 
        "action": {"url":"http://192.168.1.3/test.html"}
    }
]
```
用浏览器打开http://serverip:8010/dashboard-pro.html，点击‘开始课程’会播放课程内容。可以通过唤醒词打断提问，数字人回答完后继续播放课程

### 3.3 动作编排
- 1，切割素材
录制一段无动作和有动作衔接的视频，用剪映等软件找到衔接的时间点，用ffmepg命令切割成无动作和有动作视频。以网上的‘韵巧.mp4’为例，
```
ffmpeg -i input.mp4 -ss 00:00:00 -to 00:00:02 -c copy -copyts -avoid_negative_ts make_zero silence.mp4
ffmpeg -i input.mp4 -ss 00:00:02 -to 00:00:20 -c copy -copyts -avoid_negative_ts make_zero act.mp4
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
python genavatar.py  --video_path act.mp4  --img_size 192 --avatar_id wav2lipls_avatar1
```
- 5，运行
```
python app.py --transport webrtc --model wav2lipls --avatar_id wav2lipls_avatar1 --customvideo_config data/custom_config.json
```

### 3.4 背景透明
生成avatar时的视频背景需要用绿幕  
用拾色器获取绿幕的rgb值  
修改web/client-chat-transparency.js，将gFloor取绿幕中g的最小值、rbCeiling取绿幕中r和b的最大值。