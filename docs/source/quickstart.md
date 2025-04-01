## 2. Quick Start
- Download the models
Download the models required for running wav2lip. Link: <https://pan.quark.cn/s/83a750323ef0>  
Copy wav2lip256.pth to the "models" folder of this project and rename it to wav2lip.pth;
Extract wav2lip256_avatar1.tar.gz and copy the entire folder to the "data/avatars" folder of this project.
- Run
python app.py --transport webrtc --model wav2lip --avatar_id wav2lip256_avatar1
Open http://serverip:8010/webrtcapi.html in a browser, enter any text in the text box and submit it. The digital human will broadcast this text.

**The server side needs to open ports tcp:8010; udp:1-65536.**

If you can't access huggingface, before running:
```
export HF_ENDPOINT=https://hf-mirror.com
``` 