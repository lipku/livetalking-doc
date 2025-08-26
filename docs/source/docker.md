## 4. Docker Run  
There is no need for the previous installation. Just run it directly.
```
docker run --gpus all -it --network=host --rm registry.cn-beijing.aliyuncs.com/codewithgpu2/lipku-metahuman-stream:vjo1Y6NJ3N
```
The code is in /root/metahuman-stream. First, use `git pull` to get the latest code, and then execute the commands as in the usage section.

The following images are provided:
- autodl image: <https://www.codewithgpu.com/i/lipku/metahuman-stream/base>  
[autodl Tutorial](autodl/README.md)
- ucloud image: <https://www.compshare.cn/images/4458094e-a43d-45fe-9b57-de79253befe4?referral_code=3XW3852OBmnD089hMMrtuU&ytag=GPU_GitHub_livetalking>  
Any port can be opened, and there is no need to deploy an srs service separately.  
[ucloud Tutorial](ucloud/ucloud.md) 