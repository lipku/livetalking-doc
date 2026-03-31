
## 3. Usage
Select a digital human model, transport method, and TTS model respectively.

### 3.1 Digital Human Model
Supports 4 models: **ernerf, musetalk, wav2lip, Ultralight-Digital-Human**.  
Default: **wav2lip**


#### 3.1.1 Model: wav2lip
- Download models  
  Download required models for wav2lip:  
  <https://pan.quark.cn/s/83a750323ef0>

  - Copy `s3fd.pth` to:  
    `wav2lip/face_detection/detection/sfd/s3fd.pth`
  - Copy `wav2lip256.pth` to `models/` and rename to `wav2lip.pth`
  - Extract `wav2lip256_avatar1.tar.gz` and copy the entire folder to `data/avatars/`

- Run
  ```bash
  python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1
  ```
  Open in browser:  
  `http://serverip:8010/webrtcapi.html`

  You can set `--batch_size` to improve GPU utilization.  
  Use `--avatar_id` to run different avatars.

##### Use your own avatar
```bash
python -m avatars.wav2lip.genavatar --video_path xxx.mp4 --img_size 256 --avatar_id wav2lip256_avatar1
# img_size must be 256 for this model
# Output: data/avatars/
# If stuck, reduce --face_det_batch_size
```

**Input video must be a silent video (mouth closed, no speech).**


#### 3.1.2 Model: musetalk
- Install dependencies  
  Only required for avatar generation, not inference.
  ```bash
  conda install ffmpeg
  pip install --no-cache-dir -U openmim 
  mim install mmengine 
  mim install "mmcv>=2.0.1" 
  mim install "mmdet>=3.1.0" 
  mim install "mmpose>=1.1.0"
  ```

- Download models  
  <https://pan.xunlei.com/s/VOW3nYho64jeCxT2sxrjcE7fA1?pwd=evnw>

  - Copy files from `models/` to project `models/`
  - Extract `musetalk_avatar1.tar.gz` and copy to `data/avatars/`

- Run
  ```bash
  python app.py --transport webrtc --model musetalk --avatar_id musetalk_avatar1
  ```
  Open: `http://serverip:8010/webrtcapi.html`

##### Use your own avatar
Option 1:
```bash
git clone https://github.com/TMElyralab/MuseTalk.git
cd MuseTalk
# Set preparation: True in configs/inference/realtime.yaml
python -m scripts.realtime_inference --inference_config configs/inference/realtime.yaml
# Copy results/avatars to project data/avatars/
```

Option 2 (in livetalking project):
```bash
python -m avatars.musetalk.genavatar --avatar_id musetalk_avatar1 --file ~/sun.mp4
```
Supports video/image input. Output: `data/avatars/`

**Input video must be silent (mouth closed, no speech).**


#### 3.1.3 Model: ER-Nerf
The ernerf model is in the git branch `ernerf-rtmp`.
```bash
git checkout ernerf-rtmp
python app.py --transport webrtc --model ernerf
```

##### 3.1.3.1 Audio feature: hubert
Default: `wav2vec`. To use `hubert`:
```bash
python app.py --transport webrtc --model ernerf --asr_model facebook/hubert-large-ls960-ft
```

##### 3.1.3.2 Set head background image
```bash
python app.py --transport webrtc --model ernerf --bg_img bc.jpg
```

##### 3.1.3.3 Full-body video overlay
1. Crop training video
   ```bash
   ffmpeg -i fullbody.mp4 -vf crop="400:400:100:5" train.mp4
   ```
   Train the model with `train.mp4`.

2. Extract full-body frames
   ```bash
   ffmpeg -i fullbody.mp4 -vf fps=25 -qmin 1 -q:v 1 -start_number 0 data/fullbody/img/%08d.png
   ```

3. Run digital human
   ```bash
   python app.py --transport webrtc --model ernerf --fullbody \
   --fullbody_img data/fullbody/img \
   --fullbody_offset_x 100 --fullbody_offset_y 5 \
   --fullbody_width 580 --fullbody_height 1080 \
   --W 400 --H 400
   ```

If torso training is poor and seams are visible, add:
```bash
--torso_imgs data/xxx/torso_imgs --preload 1
```
This uses pre-extracted torso images instead of model inference.

##### Use your own avatar
Use your trained model from:  
<https://github.com/Fictionarry/ER-NeRF>  
Use `wav2vec` or `hubert` for audio features during training.

Folder structure:
```
├── data
│   ├── data_kf.json      (from transforms_train.json)
│   ├── au.csv			
│   ├── pretrained
│   └── ngp_kf.pth        (from ngp_ep00xx.pth)
```

#### 3.1.4 Model: Ultralight-Digital-Human
- Create avatar
  Train a model from:  
  <https://github.com/anliyuan/Ultralight-Digital-Human>

  Copy `checkpoint_epoch_335.pth.tar` and `scrfd_2.5g_kps.onnx` to `models/`.

  ```bash
  # Only hubert audio features are supported
  # Use a silent video for --video_path
  python -m avatars.ultralight.genavatar --video_path xxx.mp4 --avatar_id ultralight_avatar1 --checkpoint xxx.pth
  # Output: data/avatars/
  ```

- Run
  ```bash
  python app.py --transport webrtc --model ultralight --avatar_id ultralight_avatar1
  ```
  Open: `http://serverip:8010/webrtcapi.html`


### 3.2 Transport Mode
Supports `webrtc`, `rtcpush`, `rtmp`. Default: `webrtc`.

#### 3.2.1 WebRTC P2P
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1
```

**Server must open ports:**  
TCP: 8010  
UDP: 1–65536

Open: `http://serverip:8010/webrtcapi.html`


#### 3.2.2 WebRTC push to SRS
- Start SRS
  ```bash
  export CANDIDATE='<SERVER_PUBLIC_IP>'
  docker run --rm --env CANDIDATE=$CANDIDATE \
    -p 1935:1935 -p 8080:8080 -p 1985:1985 -p 8000:8000/udp \
    registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5 \
    objs/srs -c conf/rtc.conf
  ```

- Run digital human
  ```bash
  python app.py --transport rtcpush --push_url 'http://localhost:1985/rtc/v1/whip/?app=live&stream=livestream' --model wav2lip --avatar_id wav2lip256_avatar1
  ```

**Ports required:**  
TCP: 8000, 8010, 1985  
UDP: 8000

Open: `http://serverip:8010/rtcpushapi.html`  
Modify host in `rtcpushapi.html` if push URL is not localhost.

#### 3.2.3 RTMP push
- Install `rtmpstream`  
  <https://github.com/lipku/python_rtmpstream>

- Start RTMP server (SRS example)
  ```bash
  docker run --rm -it -p 1935:1935 -p 1985:1985 -p 8080:8080 registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5
  ```

- Run digital human
  ```bash
  python app.py --transport rtmp --push_url 'rtmp://localhost/live/livestream'
  ```
  Open: `http://serverip:8010/rtmpapi.html`

You can also push via `rtcpush` to SRS and convert to RTMP:
```bash
export CANDIDATE='<SERVER_PUBLIC_IP>'
docker run --rm --env CANDIDATE=$CANDIDATE \
  -p 1935:1935 -p 8080:8080 -p 1985:1985 -p 8000:8000/udp \
  registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5 \
  objs/srs -c conf/rtc2rtmp
```

### 3.3 TTS Model
Supports: `edgetts, gpt-sovits, fish-speech, xtts, cosyvoice`.  
Default: `edgetts`. Use `REF_FILE` to set voice.

#### 3.3.1 gpt-sovits
See deployment: [gpt-sovits](tts/gptsovits.md)
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --tts gpt-sovits --TTS_SERVER http://127.0.0.1:9880 --REF_FILE ref.wav --REF_TEXT xxx
```
`REF_TEXT` = content of `REF_FILE`.  
`ref.wav` must be placed on the TTS server.

#### 3.3.2 fish-speech
See deployment: [fish-speech](tts/fishspeech.md)
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --tts fishtts --TTS_SERVER http://127.0.0.1:8080 --REF_FILE test
```
`REF_FILE` = reference ID on fish-speech server.

#### 3.3.3 cosyvoice
See deployment: [cosyvoice](tts/cosyvoice.md)
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --tts cosyvoice --TTS_SERVER http://127.0.0.1:50000 --REF_FILE ref.wav --REF_TEXT xxx
```

#### 3.3.4 Tencent Cloud TTS
```bash
export TENCENT_APPID=xxx
export TENCENT_SECRET_KEY=xxx
export TENCENT_SECRET_ID=xxx
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --tts tencent --REF_FILE 101001
```
`REF_FILE` = voice ID.

#### 3.3.5 Doubao (Volcengine) TTS
```bash
export DOUBAO_APPID=xxx
export DOUBAO_TOKEN=xxx
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --tts doubao --REF_FILE zh_female_roumeinvyou_emo_v2_mars_bigtts
```

#### 3.3.6 Alibaba Qwen TTS
```bash
export DASHSCOPE_API_KEY=<your_api_key>
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --tts qwen --REF_FILE Cherry
```

#### 3.3.7 XTTS
Start XTTS server:
```bash
docker run --gpus=all -e COQUI_TOS_AGREED=1 --rm -p 9000:80 ghcr.io/coqui-ai/xtts-streaming-server:latest
```
Run:
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --tts xtts --REF_FILE data/ref.wav --TTS_SERVER http://localhost:9000
```

### 3.4 Action Choreography
1. Generate assets
   ```bash
   ffmpeg -i xxx.mp4 -vf fps=25 -qmin 1 -q:v 1 -start_number 0 data/customvideo/image/%08d.png
   ffmpeg -i xxx.mp4 -vn -acodec pcm_s16le -ac 1 -ar 16000 data/customvideo/audio.wav
   ```

2. Edit `data/custom_config.json`  
   Set `imgpath`, `audiopath`, and `audiotype`:
   - 0: inference video
   - 1: silent video
   - ≥2: custom config

3. Run
   ```bash
   python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --customvideo_config data/custom_config.json
   ```

4. Open  
   `http://<serverip>:8010/webrtcapi-custom.html`  
   Enter `audiotype` to switch videos. Silent videos switch automatically.


### 3.5 LLM Dialogue
Currently uses Qwen API (OpenAI-compatible). Supports streaming output.  
Modify `llm.py` to connect other LLMs.
```bash
export DASHSCOPE_API_KEY=<your_api_key>
```
Open:
- `http://serverip:8010/rtcpushchat.html`
- `http://serverip:8010/webrtcchat.html`


### 3.6 Multi-Concurrency
```bash
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1 --max_session 3
```
Open multiple `webrtcapi.html` tabs.


### 3.7 Audio Input
1. FunASR speech recognition  
   Open `webrtcapi-asr.html` or `rtcpushapi-asr.html`.  
   Click **start** → connect → begin audio capture.

   If browser blocks mic:
   ```
   edge://flags/#unsafely-treat-insecure-origin-as-secure
   ```
   Add your server URL and restart browser.

   FunASR server:  
   <https://github.com/modelscope/FunASR/blob/main/runtime/python/websocket/README.md>

2. Browser built-in ASR (with LLM chat)
   ```bash
   export DASHSCOPE_API_KEY=<your_api_key>
   python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1
   ```
   Open `dashboard.html` (add to browser secure origin whitelist first).


### 3.8 Virtual Camera Output
Install virtual camera: <https://github.com/letmaik/pyvirtualcam>
```bash
pip install pyvirtualcam
pip install pyaudio
python app.py --transport virtualcam --model wav2lip --avatar_id wav2lip256_avatar1
```
Open OBS or other streaming software, select the virtual camera as input.  
Open `webrtcapi.html`, **do NOT click start** — just type text and send.
