
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

