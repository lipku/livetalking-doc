# FAQ
1.  pytorch3d安装不成功\
    下载源码编译

```bash
git clone https://github.com/facebookresearch/pytorch3d.git
python setup.py install
```

2.  websocket连接报错\
    修改python/site-packages/flask\_sockets.py

```python
self.url_map.add(Rule(rule, endpoint=f)) 改成 
self.url_map.add(Rule(rule, endpoint=f, websocket=True))
```

3. protobuf版本过高

```bash
pip uninstall protobuf
pip install protobuf==3.20.1
```

4. ernerf数字人不眨眼\
训练模型时添加如下步骤

> Obtain AU45 for eyes blinking.\
> Run FeatureExtraction in OpenFace, rename and move the output CSV file to data/\<ID>/au.csv.

将au.csv拷到本项目的data目录下


5. 用自己训练的模型报错维度不匹配\
训练模型时用wav2vec提取音频特征

```bash
python main.py data/ --workspace workspace/ -O --iters 100000 --asr_model cpierse/wav2vec2-large-xlsr-53-esperanto
```

6. rtmp推流时ffmpeg版本不对
网上版友反馈是需要4.2.2版本。我也不确定具体哪些版本不行。原则是运行一下ffmpeg，打印的信息里需要有libx264，如果没有肯定不行
```
--enable-libx264
```
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
conda install ffmpeg
```
7. 替换自己训练的模型
```python
.
├── data
│   ├── data_kf.json （对应训练数据中的transforms_train.json）
│   ├── au.csv			
│   ├── pretrained
│   └── └── ngp_kf.pth （对应训练后的模型ngp_ep00xx.pth）

```
8. musetalk预处理数字人报错
```
'Adafactor is already registered in optimizer at torch.optim'
```
将pip版本降低到24.0
```
python -m pip install pip==24.0
```
然后再重新运行
```
mim install "mmdet>=3.1.0" 
mim install "mmpose>=1.1.0"
```

9. numpy版本不兼容  
报错：numpy.dtype size changed, may indicate binary incompatibility. Expected 96 from C header, got 88 from PyObject  
解决：降低numpy版本
```
pip install --upgrade "numpy==1.26.4"
```

其他参考
https://github.com/lipku/metahuman-stream/issues/43#issuecomment-2008930101


