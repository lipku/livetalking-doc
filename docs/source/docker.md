## 4. Docker Run  
不需要前面的安装，直接运行。
```
docker run --gpus all -it --network=host --rm registry.cn-beijing.aliyuncs.com/codewithgpu2/lipku-metahuman-stream:vjo1Y6NJ3N
```
代码在/root/metahuman-stream，先git pull拉一下最新代码，然后执行命令同usage

提供如下镜像
- autodl镜像：<https://www.codewithgpu.com/i/lipku/metahuman-stream/base>  
[autodl教程](autodl/README.md)
- ucloud镜像 <https://www.compshare.cn/images-detail?ImageID=compshareImage-14pa8x8ucwr9&ImageType=Community&referral_code=3XW3852OBmnD089hMMrtuU&ytag=lipku_github>  
可以开放任意端口，不需要单独部署srs服务  
[ucloud教程](ucloud/ucloud.md) 
