# 7. Paid Services  

WeChat: wxwubug (please note your requirements)  
Email: lipku@foxmail.com  
Telegram: https://t.me/livetalking  

## 7.1 HD Version of wav2lip  
The wav2lip network structure has been upgraded and optimized: the audio feature has been changed to Hubert, and an LSTM network has been integrated to establish connections between preceding and following frames.  

It is available in two versions:  
1. **192 Version**  
   Achieves real-time performance on an RTX 3060 GPU. Price: ¥2,000.  

2. **384 Version**  
   Achieves real-time performance on an RTX 4070 GPU. Price: ¥3,000. This price includes both the 192 and 384 versions of the model. Users who have already purchased the 192 version can upgrade to the 384 version by paying an additional ¥1,000.  

P.S.: The HD model improves lip movement accuracy and tooth clarity, **not** the video resolution itself. To enhance video definition, you need to increase the resolution of the original video and modify the h264.py file in aiortc to raise the encoding bitrate.  


## 7.2 Feature Optimization  
1. **Unlimited Duration for Avatar Videos**  
   Manages image memory through caching, loading only the most recently needed images to significantly reduce memory usage. Supports avatar videos of unlimited duration, suitable for specific scenarios such as live streaming.  

2. **Vue Client**  
   Provides Vue frontend code and supports a transparent background for the digital human. Client download link: <https://pan.quark.cn/s/d7192d8ac19b>  

3. **Python Client**  
   Displays video in an independent window and offers Python API interaction, making it easier to integrate with Python projects (e.g., live streaming scenarios).  

4. **Voice Interaction**  
   (1) Interrupt the digital human’s speech to ask a question using a wake word.  
   (2) Prevents the digital human from being disturbed by background noise while speaking.  
   (3) Allows follow-up questions after the digital human answers a question.  

5. **Real-Time Avatar Switching**  
   Enables real-time switching of avatar appearances during interaction, ideal for performing different actions in various scenarios.  


## 7.3 Price List  

| Feature | Price | Remarks |  
| :--- | :--- | :--- |  
| HD wav2lip Model | ¥3,000 | [Demo Video](./assets/wav2lipls384.MP4) |  
| Unlimited-Duration Avatar (Currently only supports wav2lip) | ¥3,000 | - |  
| Transparent Background + Vue Code | ¥3,000 | [Demo Video](https://www.bilibili.com/video/BV1CYKWzzEzs/) |  
| Voice Interaction, Synchronized Subtitles & Event Notifications, Vue Code | ¥8,000 | [Demo Video](https://www.bilibili.com/video/BV1ABKUzVEdP) |  
| Assign Unique Avatar & Voice to Each Connection | ¥1,000 | - |  
| Real-Time Audio Stream Input | ¥2,000 | - |  
| Real-Time Avatar & Action Switching | ¥5,000 | [Demo Video](https://www.bilibili.com/video/BV1mMpEzuEty/) |  
| Python Client | ¥4,000 | [Demo Video](https://www.bilibili.com/video/BV13va7zNEVv) |