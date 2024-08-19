## api接口
客户端通过http post请求与后台交互

### 5.1 发送文字
- **接口说明：** 向数字人发送聊天内容
- **接口地址：** /human

#### 5.1.1 请求参数
  
paramter			|type		|required	|desc  
:----				|:---		|:------	|:---	
sessionid			|int		|O			|数字人会话id，默认为0
interrupt			|bool		|O			|是否打断数字人当前说话，默认为false
type				|string		|R			|echo：数字人播报输入文字；chat：与数字人对话
text				|string		|R			|文字内容


请求示例：

```
{
    ‘text’: ‘hello’,
    ‘type’: 'echo',
    ‘interrupt’: true,
    ‘sessionid’:0
}

```

### 5.2 切换播放视频
- **接口说明：** 控制数字人播放自定义视频
- **接口地址：** /set_audiotype

#### 5.2.1 请求参数
  
paramter			|type		|required	|desc  
:----				|:---		|:------	|:---	
sessionid			|int		|O			|数字人会话id，默认为0
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