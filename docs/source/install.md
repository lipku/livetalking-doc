## 1. Installation


### 1.1 Install dependency

1. 按照开源版本安装环境
2. 解压源码包，将高清模型拷到代码models目录下
3. 下载hubert模型放到models目录下
```python
import huggingface_hub
from huggingface_hub import snapshot_download
huggingface_hub.login(token) # token 从 https://huggingface.co/settings/tokens 获取
snapshot_download('facebook/hubert-large-ls960-ft', local_dir='models/hubert-large-ls960-ft')
```
4. 添加加密解析库
```bash
pip install pyarmor
pyarmor gen test.py  #随便写个py文件即可
```
在当前目录的dist/pyarmor_runtime_000000拷到源码根目录下
