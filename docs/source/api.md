# 5. API Interface

The client interacts with the backend via HTTP POST requests.

### 5.1 Get Video

- **Interface Description**: Negotiate and obtain WebRTC video stream

- **Endpoint**: `/offer`

#### 5.1.1 Request Parameters

|Parameter|Type|Required|Description|
|---|---|---|---|
|type|string|Y|Must be `offer`|
|sdp|string|Y|WebRTC offer parameter|
|avatar|string|N|Avatar ID; default value is `avatar_id` from startup command parameters|
|refaudio|string|N|Cloned voice ID; default value is `REF_FILE` from startup command parameters|
|reftext|string|N|Text for cloned voice; default value is `REF_TEXT` from startup command parameters|
|custom_config|string|N|JSON string for action choreography configuration; default value is `customvideo_config` from startup command parameters|
Request example:

```JSON

{
    "sdp": "xxxx",
    "type": "offer"
}
```

#### 5.1.2 Response Data

|Parameter|Type|Required|Description|
|---|---|---|---|
|type|string|Y|Must be `answer`|
|sdp|string|Y|WebRTC answer parameter|
|sessionid|string|Y|Digital human session ID, used to distinguish multiple digital human streams. Required for all subsequent interfaces.|
Response example:

```JSON

{
    "sdp": "xxxx",
    "type": "answer",
    "sessionid": "d39256ea-c017-4cdc-8d4b-b73fd246d95f"
}
```

### 5.2 Send Text

- **Interface Description**: Send chat content to the digital human

- **Endpoint**: `/human`

#### 5.2.1 Request Parameters

|Parameter|Type|Required|Description|
|---|---|---|---|
|sessionid|string|Y|Digital human session ID|
|interrupt|bool|N|Whether to interrupt the digital human’s current speech; default is `false`|
|type|string|Y|`echo`: Digital human speaks the input text; `chat`: Chat with the digital human|
|text|string|Y|Text content|
Request example:

```JSON

{
    "text": "hello",
    "type": "echo",
    "interrupt": true,
    "sessionid": "d39256ea-c017-4cdc-8d4b-b73fd246d95f"
}
```

### 5.3 Switch Playback Video

- **Interface Description**: Control the digital human to play custom video

- **Endpoint**: `/set_audiotype`

#### 5.3.1 Request Parameters

|Parameter|Type|Required|Description|
|---|---|---|---|
|sessionid|string|Y|Digital human session ID|
|audiotype|int|Y|Video content to play, corresponding to custom videos configured on the backend|
Request example:

```JSON

{
    "audiotype": 2,
    "sessionid": "d39256ea-c017-4cdc-8d4b-b73fd246d95f"
}
```

### 5.4 Recording

- **Interface Description**: Save digital human video recording on the server; file path: `data/record.mp4`

- **Endpoint**: `/record`

#### 5.4.1 Request Parameters

|Parameter|Type|Required|Description|
|---|---|---|---|
|sessionid|string|Y|Digital human session ID|
|type|string|Y|`start_record`: Start recording; `end_record`: Stop recording|
Request example:

```JSON

{
    "type": "start_record",
    "sessionid": "d39256ea-c017-4cdc-8d4b-b73fd246d95f"
}
```

### 5.5 Interrupt Digital Human Speech

- **Interface Description**: Interrupt the digital human’s current speech

- **Endpoint**: `/interrupt_talk`

#### 5.5.1 Request Parameters

|Parameter|Type|Required|Description|
|---|---|---|---|
|sessionid|string|Y|Digital human session ID|
Request example:

```JSON

{
    "sessionid": "d39256ea-c017-4cdc-8d4b-b73fd246d95f"
}
```

### Third-Party Software Integration Process

```JavaScript

pc = new RTCPeerConnection();
var offer = pc.localDescription;
Call the API `/offer` with parameters `offer.sdp` and `offer.type`.
Obtain the response data `answer`.
pc.setRemoteDescription(answer); // Set the response to the PC object; video stream will be received afterward.
// For detailed code, refer to web/client.js

Call the API `/human` with `sessionid` from `answer.sessionid` to trigger the digital human to speak.
```
