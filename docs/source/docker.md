## 4. Docker Run  
不需要前面的安装，直接运行。
```
docker run --gpus all -it --network=host --rm registry.cn-zhangjiakou.aliyuncs.com/codewithgpu3/lipku-livetalking:toza2irpHZ
```
代码在/root/livetalking，先git pull拉一下最新代码，然后执行命令同usage

提供如下镜像
- autodl镜像：<https://www.codewithgpu.com/i/lipku/livetalking/base>  
[autodl教程](autodl/README.md)
- ucloud镜像 <https://www.compshare.cn/images/4458094e-a43d-45fe-9b57-de79253befe4?referral_code=3XW3852OBmnD089hMMrtuU&ytag=GPU_GitHub_livetalking>  
可以开放任意端口，不需要单独部署srs服务  
[ucloud教程](ucloud/ucloud.md) 
