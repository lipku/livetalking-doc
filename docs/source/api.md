## 5. API Interfaces
The client interacts with the backend through HTTP POST requests.

### 5.1 Get Video
- **Interface Description:** Negotiate to obtain the WebRTC video.
- **Interface Address:** /offer

#### 5.1.1 Request Parameters

| Parameter | Type | Required | Description |
| :---- | :--- | :------ | :--- |
| type | string | R | Fill in "offer" |
| sdp | string | R | WebRTC request parameters |

Request Example:

```
{
    "sdp": "xxxx",
    "type": "offer"
}
```

#### 5.1.2 Returned Data

| Parameter | Type | Required | Description |
| :---- | :--- | :------ | :--- |
| type | string | R | Fill in "answer" |
| sdp | string | R | WebRTC response parameters |
| sessionid | int | R | Digital human session ID, used to distinguish multiple digital humans. This parameter is required for subsequent interfaces. |

Return Example:

```
{
    "sdp": "xxxx",
    "type": "answer",
    "sessionid": 1
}
```

### 5.2 Send Text
- **Interface Description:** Send chat content to the digital human.
- **Interface Address:** /human

#### 5.2.1 Request Parameters

| Parameter | Type | Required | Description |
| :---- | :--- | :------ | :--- |
| sessionid | int | O | Digital human session ID, default is 0 |
| interrupt | bool | O | Whether to interrupt the digital human's current speech, default is false |
| type | string | R | echo: The digital human broadcasts the input text; chat: Have a conversation with the digital human |
| text | string | R | Text content |

Request Example:

```
{
    "text": "hello",
    "type": "echo",
    "interrupt": true,
    "sessionid": 0
}
```

### 5.3 Switch Playing Video
- **Interface Description:** Control the digital human to play a custom video.
- **Interface Address:** /set_audiotype

#### 5.3.1 Request Parameters

| Parameter | Type | Required | Description |
| :---- | :--- | :------ | :--- |
| sessionid | int | O | Digital human session ID, default is 0 |
| audiotype | int | R | The content of the video to be played, corresponding to the custom video on the backend |
| reinit | bool | R | Whether to play the switched video from the beginning, false means to continue from where it left off last time |

Request Example:

```
{
    "audiotype": 2,
    "reinit": false,
    "sessionid": 0
}
```

### 5.4 Record Video
- **Interface Description:** Save the digital human's video recording on the server side, and the file address is data/record.mp4.
- **Interface Address:** /record

#### 5.4.1 Request Parameters

| Parameter | Type | Required | Description |
| :---- | :--- | :------ | :--- |
| sessionid | int | O | Digital human session ID, default is 0 |
| type | string | R | start_record: Start recording; end_record: Stop recording |

Request Example:

```
{
    "type": "start_record",
    "sessionid": 0
}
```

### Third-Party Software Docking Process
```javascript
pc = new RTCPeerConnection();
var offer = pc.localDescription;
Call the api /offer interface and pass in the parameters offer.sdp and offer.type. Obtain the returned data answer.
pc.setRemoteDescription(answer); // Set the returned data into pc, and then you can receive the video. Refer to the specific code in web/client.js for this part.

Call the api /human interface, and the sessionid is answer.sessionid. Drive the digital human to speak.
``` 