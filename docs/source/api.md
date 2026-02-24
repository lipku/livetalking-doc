## 5. api接口
客户端通过http post请求与后台交互

### 5.1 获取视频
- **接口说明：** 协商获取webrtc视频
- **接口地址：** /offer

#### 5.1.1 请求参数
  
paramter			|type		|required	|desc  
:----				|:---		|:------	|:---	
type				|string		|R			|填offer
sdp				    |string		|R			|webrtc请求参数
avatar				|string		|O			|avatarid,默认值用命令启动参数里的avatar_id
refaudio			|string		|O			|克隆音色id，默认值用命令启动参数里的REF_FILE
reftext 			|string		|O			|克隆音色文本，默认值用命令启动参数里的REF_TEXT
llmid			    |string		|O			|用于区分不同rag，默认值为''
custom_config		|string		|O			|动作编排配置json字符串，默认值用命令启动参数里的customvideo_config
multiavatar_config	|string		|O			|avatar切换配置json字符串，默认值用命令启动参数里的multiavatar_config


请求示例：

```
{
    ‘sdp’: ‘xxxx’,
    ‘type’: 'offer',
}

```
#### 5.1.2 返回数据
  
paramter			|type		|required	|desc  
:----				|:---		|:------	|:---	
type				|string		|R			|填answer
sdp				    |string		|R			|webrtc应答参数
sessionid			|int		|R			|数字人会话id，用于区分多路数字人。后面的接口都需要用该参数


返回示例：

```
{
    ‘sdp’: ‘xxxx’,
    ‘type’: 'answer',
    ‘sessionid’: 1,
}

```

### 5.2 发送文字
- **接口说明：** 向数字人发送聊天内容
- **接口地址：** /human

#### 5.2.1 请求参数
  
paramter			|type		|required	|desc  
:----				|:---		|:------	|:---	
sessionid			|int		|R			|数字人会话id
interrupt			|bool		|O			|是否打断数字人当前说话，默认为false
type				|string		|R			|echo：数字人播报输入文字；chat：与数字人对话
text				|string		|R			|文字内容
avatarid			|string		|O			|选择用哪个avatar播报，与multiavatar_config中对应，默认用主avatar('main')
tts			        |string		|O			|配置tts的参考声音和参考文本,默认用命令行参数配置的ref_file和ref_text


请求示例：

```
{
    ‘text’: ‘hello’,
    ‘type’: 'echo',
    ‘interrupt’: true,
    ‘sessionid’:0,
    'avatarid':'wav2lip256_action',
    'tts':{'ref_file':'xxx','ref_text':'xxx'}
}

```

### 5.3 切换播放视频
- **接口说明：** 控制数字人播放自定义视频
- **接口地址：** /set_audiotype

#### 5.3.1 请求参数
  
paramter			|type		|required	|desc  
:----				|:---		|:------	|:---	
sessionid			|int		|R			|数字人会话id
audiotype			|int		|R			|播放的视频内容，与后台自定义视频对应
reinit				|bool		|R			|切换的视频是否从头开始播，false为继续上次的播放


请求示例：

```
{
    ‘audiotype’: 2,
    ‘reinit’: false,
    ‘sessionid’:0
}

```

### 5.4 录像
- **接口说明：** 在服务端保存数字人录像,文件地址data/record.mp4
- **接口地址：** /record

#### 5.4.1 请求参数
  
paramter			|type		|required	|desc  
:----				|:---		|:------	|:---	
sessionid			|int		|R			|数字人会话id
type   			    |string		|R			|start_record:开始录像; end_record:停止录像


请求示例：

```
{
    ‘type’: 'start_record',
    ‘sessionid’:0
}

```

### 第三方软件对接流程
```javascript
pc = new RTCPeerConnection();
var offer = pc.localDescription;
调用api /offer接口，传入参数为offer.sdp, offer.type。获取返回数据answer
pc.setRemoteDescription(answer) #将返回数据设置到pc中，后面就能接收到视频。 此部分具体代码参考web/client.js

调用api /human接口, sessionid为answer.sessionid。驱动数字人说话

```