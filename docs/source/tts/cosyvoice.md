
## 部署tts推理
git clone --recursive https://github.com/FunAudioLLM/CosyVoice.git
## 1. 安装依赖库
```
conda create -n cosyvoice python=3.10
conda activate cosyvoice
# pynini is required by WeTextProcessing, use conda to install it as it can be executed on all platform.
conda install -y -c conda-forge pynini==2.1.5
pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/ --trusted-host=mirrors.aliyun.com

```

## 2. 下载模型文件
在CosyVoice目录下执行
```python
from modelscope import snapshot_download
snapshot_download('iic/CosyVoice2-0.5B', local_dir='pretrained_models/CosyVoice2-0.5B')
snapshot_download('iic/CosyVoice-ttsfrd', local_dir='pretrained_models/CosyVoice-ttsfrd')
#snapshot_download('iic/CosyVoice-300M', local_dir='pretrained_models/CosyVoice-300M')
#snapshot_download('iic/CosyVoice-300M-SFT', local_dir='pretrained_models/CosyVoice-300M-SFT')
```

``` sh
cd pretrained_models/CosyVoice-ttsfrd/
unzip resource.zip -d .
pip install ttsfrd_dependency-0.1-py3-none-any.whl
pip install ttsfrd-0.4.2-cp310-cp310-linux_x86_64.whl
```

## 3. 启动api服务: 
```
cd CosyVoice/runtime/python/fastapi/
python server.py --model_dir ../../../pretrained_models/CosyVoice2-0.5B

# 测试
# python client.py --mode zero_shot
```


