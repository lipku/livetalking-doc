## 3. Usage
Select the digital human model, transmission method, and TTS model respectively.

### 3.1 Digital Human Model
Supports 4 models: ernerf, musetalk, wav2lip, Ultralight-Digital-Human

#### 3.1.1 Using the wav2lip Model
RTMP pushing is not supported.
- Download the model
Download the model required for running wav2lip. Link: <https://pan.baidu.com/s/1yOsQ06-RIDTJd3HFCw4wtA> Password: ltua
Copy s3fd.pth to wav2lip/face_detection/detection/sfd/s3fd.pth of this project;
Copy wav2lip256.pth to the "models" folder of this project and rename it to wav2lip.pth;
Extract wav2lip256_avatar1.tar.gz and copy the entire folder to the "data/avatars" folder of this project.
- Run
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1
Open http://serverip:8010/webrtcapi.html in a browser.
You can set --batch_size to improve the GPU utilization rate, and set --avatar_id to run different digital humans.
##### Replace with Your Own Digital Human
```bash
cd wav2lip
python genavatar.py --video_path xxx.mp4 --img_size 256 --avatar_id wav2lip256_avatar1
After running, copy the files under results/avatars to the "data/avatars" folder of this project.
```

#### 3.1.2 Using the musetalk Model
RTMP pushing is not supported.
- Install dependent libraries
```bash
conda install ffmpeg
pip install --no-cache-dir -U openmim 
mim install mmengine 
mim install "mmcv>=2.0.1" 
mim install "mmdet>=3.1.0" 
mim install "mmpose>=1.1.0"
```
- Download the model
Download the model required for running MuseTalk. A download address is provided: <https://caiyun.139.com/m/i?2eAjs2nXXnRgr>  Extraction code: qdg2
After decompression, copy the files under "models" to the "models" folder of this project.
Download the digital human model. Link: <https://caiyun.139.com/m/i?2eAjs8optksop>  Extraction code: 3mkt. After decompression, copy the entire folder to the "data/avatars" folder of this project.
- Run
python app.py --model musetalk --transport webrtc
Open http://serverip:8010/webrtcapi.html in a browser.
You can set --batch_size to improve the GPU utilization rate, and set --avatar_id to run different digital humans.
##### Replace with Your Own Digital Human
```bash
git clone https://github.com/TMElyralab/MuseTalk.git
cd MuseTalk
Modify configs/inference/realtime.yaml and change preparation to True.
python -m scripts.realtime_inference --inference_config configs/inference/realtime.yaml
After running, copy the files under results/avatars to the "data/avatars" folder of this project.
```
OR
```bash
cd musetalk 
python simple_musetalk.py --avatar_id 4  --file D:\\ok\\test.mp4
Supports the generation of videos and images, which will be automatically generated in the "avatars" directory of the "data" folder.
```

#### 3.1.3 ER-Nerf
```
python app.py --transport webrtc --model ernerf
```
The following parameter configurations are supported:
##### 3.1.3.1 Using hubert for Audio Features
By default, wav2vec is used. If hubert is used to extract audio features when training the model, start the digital human with the following command:
```
python app.py --asr_model facebook/hubert-large-ls960-ft 
```

##### 3.1.3.2 Set the Head Background Image
```
python app.py --bg_img bc.jpg 
```

##### 3.1.3.3 Pasting Back the Full-Body Video
- 1. Cut the training video
```
ffmpeg -i fullbody.mp4 -vf crop="400:400:100:5" train.mp4 
```
Use train.mp4 to train the model.
- 2. Extract the full-body images
```
ffmpeg -i fullbody.mp4 -vf fps=25 -qmin 1 -q:v 1 -start_number 0 data/fullbody/img/%08d.png
```
- 3. Start the digital human
```
python app.py --fullbody --fullbody_img data/fullbody/img --fullbody_offset_x 100 --fullbody_offset_y 5 --fullbody_width 580 --fullbody_height 1080 --W 400 --H 400
```
- --fullbody_width and --fullbody_height are the width and height of the full-body video.
- --W and --H are the width and height of the training video.
- If the third step (torso) of ernerf training is not well-trained, there will be seams at the splicing position. You can add --torso_imgs data/xxx/torso_imgs --preload 1 to the above command, and the torso does not need model inference, but directly uses the torso images in the training dataset. This method may have some artificial traces at the head and neck.

##### Replace with Your Own Digital Human
Replace it with your own trained model <https://github.com/Fictionarry/ER-NeRF>. When training the model, the audio features should be selected as wav2vec or hubert.
```bash
├── data
│   ├── data_kf.json (Corresponds to transforms_train.json in the training data)
│   ├── au.csv			
│   ├── pretrained
│   └── └── ngp_kf.pth (Corresponds to the trained model ngp_ep00xx.pth)

```

#### 3.1.4 Using the Ultralight-Digital-Human Model
RTMP pushing is not supported.
- Create an avatar
First, train the model according to the project <https://github.com/anliyuan/Ultralight-Digital-Human>, and then in this project:
```bash
cd ultralight
python genavatar.py --dataset data_dir/  --checkpoint xxx.pth  #data_dir is the folder after data processing during training
After running, copy the files under results/avatars to the "data/avatars" folder of this project.
```
- Run
python app.py --transport webrtc --model ultralight --avatar_id ultralight_avatar1
Open http://serverip:8010/webrtcapi.html in a browser.
You can set --batch_size to improve the GPU utilization rate, and set --avatar_id to run different digital humans.
The test results are average. Mainly, the mouth is unstable when not speaking. If there is anything wrong with the usage, suggestions are welcome.

### 3.2 Transmission Modes
Supports webrtc, rtcpush, rtmp. The default is rtcpush.
#### 3.2.1 webrtc p2p
This mode does not require SRS.
```
python app.py --transport webrtc
```
**The server side needs to open ports tcp:8010; udp:1-65536.**
Open http://serverip:8010/webrtcapi.html in a browser.

#### 3.2.2 Pushing webrtc to SRS
- Start SRS
```
export CANDIDATE='<External IP of the Server>'
docker run --rm --env CANDIDATE=$CANDIDATE \
  -p 1935:1935 -p 8080:8080 -p 1985:1985 -p 8000:8000/udp \
  registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5 \
  objs/srs -c conf/rtc.conf
```
- Run the digital human
```python
python app.py --transport rtcpush --push_url 'http://localhost:1985/rtc/v1/whip/?app=live&stream=livestream'
```
**The server side needs to open ports tcp:8000, 8010, 1985; udp:8000.**
Open http://serverip:8010/rtcpushapi.html in a browser.

#### 3.2.3 Pushing rtmp to SRS
- Install the rtmpstream library
<https://github.com/lipku/python_rtmpstream>

- Start SRS
```
docker run --rm -it -p 1935:1935 -p 1985:1985 -p 8080:8080 registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5
```
- Run the digital human
```python
python app.py --transport rtmp --push_url 'rtmp://localhost/live/livestream'
```
Open http://serverip:8010/echoapi.html in a browser.

**rtmp can also be pushed to SRS through rtcpush, and SRS will convert it into an rtmp stream.**
```bash
export CANDIDATE='<External IP of the Server>' #If SRS and the browser access are on the same-level internal network, this step does not need to be executed.
docker run --rm --env CANDIDATE=$CANDIDATE \
  -p 1935:1935 -p 8080:8080 -p 1985:1985 -p 8000:8000/udp \
  registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5 \
  objs/srs -c conf/rtc2rtmp
```

### 3.3 TTS Model
Supports edgetts, gpt-sovits, fish-speech, xtts, cosyvoice. The default is edgetts.
#### 3.3.1 gpt-sovits
For service deployment, refer to [gpt-sovits](tts/gptsovits.md)
Run
```
python app.py --tts gpt-sovits --TTS_SERVER http://127.0.0.1:9880 --REF_FILE ref.wav --REF_TEXT xxx
```
REF_TEXT is the speech content in REF_FILE, and the duration should not be too long. The wav file here needs to be placed on the TTS server side, which is the path relative to the TTS service.

#### 3.3.2 fish-speech
For service deployment, refer to [fish-speech](tts/fishspeech.md)
Run
```
python app.py --tts fishtts --TTS_SERVER http://127.0.0.1:8080 --REF_FILE test
```
--REF_FILE is the referenceid of the fish-speech server side.

#### 3.3.3 cosyvoice
For service deployment, refer to [cosyvoice](tts/cosyvoice.md)
Run
```
python app.py --tts cosyvoice --TTS_SERVER http://127.0.0.1:50000 --REF_FILE ref.wav --REF_TEXT xxx
```
REF_TEXT is the speech content in REF_FILE, and the duration should not be too long.

#### 3.3.4 Tencent Speech Service
Run
```shell
export TENCENT_APPID=xxx #appid
export TENCENT_SECRET_KEY=xxx  #seceret_key
export TENCENT_SECRET_ID=xxx #seceret_id
python app.py --tts tencent  --REF_FILE 101001
```
REF_FILE is the tone ID. You can check the tone list at <https://cloud.tencent.com/document/product/1073/92668>, or it can be the tone ID of your own cloned voice.

#### 3.3.5 xtts
Run the xtts service, refer to <https://github.com/coqui-ai/xtts-streaming-server>
```
docker run --gpus=all -e COQUI_TOS_AGREED=1 --rm -p 9000:80 ghcr.io/coqui-ai/xtts-streaming-server:latest
```
Then run, where ref.wav is the sound file to be cloned.
```
python app.py --tts xtts --REF_FILE data/ref.wav --TTS_SERVER http://localhost:9000
```

### 3.4 Video Arrangement
- 1. Generate materials
```
ffmpeg -i xxx.mp4 -s 576x768 -vf fps=25 -qmin 1 -q:v 1 -start_number 0 data/customvideo/image/%08d.png
ffmpeg -i xxx.mp4 -vn -acodec pcm_s16le -ac 1 -ar 16000 data/customvideo/audio.wav
```
Among them, -s is consistent with the size of the inference output video.
- 2. Edit data/custom_config.json
Specify imgpath and audiopath.
Set audiotype. Explanation: 0 represents the inference video and does not need to be set; 1 represents the silent video. If not set, the inference video will be used by default; custom configurations are above 2.
- 3. Run
```
python app.py --transport webrtc --customvideo_config data/custom_config.json
```
- 4. Open http://<serverip>:8010/webrtcapi-custom.html
Fill in the audiotype configured in custom_config.json and click to switch the video. If it is a silent video with audiotype 1, it will be automatically switched without manual clicking.

### 3.5 Using the LLM Model for Digital Human Dialogue

Currently, it is implemented by calling the Qwen large model API, which is compatible with the OpenAI interface. It supports the streaming output of the LLM.
```
export DASHSCOPE_API_KEY=<your_api_key>
```

Open http://serverip:8010/rtcpushchat.html or http://serverip:8010/webrtcchat.html in a browser according to the transmission mode.

### 3.6 Multiple Sessions
```
python app.py --transport webrtc  --max_session 3 
```
Specify the maximum number of sessions to run through max_session. Then open multiple webrtcapi.html.

### 3.7 Voice Input
Open webrtcapi-asr.html or rtcpushapi-asr.html according to the webrtc or rtcpush transmission mode respectively.
First, click the start button at the top to connect to the video; then click the connect and start buttons of the voice collection box below; start voice collection and drive the digital human to broadcast (there is no need to click the stop button after speaking, just continue to speak the next sentence after the digital human finishes speaking).
If the browser cannot collect the voice, enter edge://flags/#unsafely-treat-insecure-origin-as-secure in the browser address bar, enter the server address in the following box and restart the browser.
![img.png](./assets/audio-input-browser.jpg)
If you need to install your own ASR server, please refer to <https://github.com/modelscope/FunASR/blob/main/runtime/python/websocket/README.md>

### 3.8 Integration of More Functions
- Voice input, RAG [Fay](https://github.com/xszyou/Fay)
- Virtual anchor, subtitle capture [Luna](https://github.com/Ikaros-521/AI-Vtuber) 