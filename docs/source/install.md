## 1. Installation

Tested on Ubuntu 20.04, Python3.10, Pytorch 1.12 and CUDA 11.3

### 1.1 Install dependency

```bash
conda create -n nerfstream python=3.10
conda activate nerfstream
# If the CUDA version is not 11.3 (confirm the version by running nvidia-smi), install the corresponding version of PyTorch according to <https://pytorch.org/get-started/previous-versions/> 
conda install pytorch==1.12.1 torchvision==0.13.1 cudatoolkit=11.3 -c pytorch
pip install -r requirements.txt
# If you are not training the ernerf model, there is no need to install the following libraries
pip install "git+https://github.com/facebookresearch/pytorch3d.git"
pip install tensorflow-gpu==2.8.0
pip install --upgrade "protobuf<=3.20.1"
``` 
Common installation issues can be found in [FAQ](faq.md).  
For setting up the Linux CUDA environment, you can refer to [this article](https://zhuanlan.zhihu.com/p/674972886). 