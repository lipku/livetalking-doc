# FAQ
1. pytorch3d fails to install
Download the source code and compile it.

```bash
git clone https://github.com/facebookresearch/pytorch3d.git
python setup.py install
```

2. Websocket connection error
Modify python/site-packages/flask_sockets.py

```python
Change self.url_map.add(Rule(rule, endpoint=f)) to 
self.url_map.add(Rule(rule, endpoint=f, websocket=True))
```

3. The version of protobuf is too high

```bash
pip uninstall protobuf
pip install protobuf==3.20.1
```

4. The ernerf digital human doesn't blink
Add the following steps when training the model:

> Obtain AU45 for eyes blinking.
> Run FeatureExtraction in OpenFace, rename and move the output CSV file to data/<ID>/au.csv.

Copy the au.csv to the data directory of this project.

5. Error of dimension mismatch when using a self-trained model
Use wav2vec to extract audio features when training the model.

```bash
python main.py data/ --workspace workspace/ -O --iters 100000 --asr_model cpierse/wav2vec2-large-xlsr-53-esperanto
```

6. The ffmpeg version is incorrect when pushing the stream via rtmp
According to the feedback from online friends, the version 4.2.2 is required. I'm not sure which specific versions don't work. The principle is to run ffmpeg, and the printed information should contain libx264. If it doesn't, it definitely won't work.
```
--enable-libx264
```
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
conda install ffmpeg
```
7. Replace with a self-trained model
```python
.
├── data
│   ├── data_kf.json (Corresponds to transforms_train.json in the training data)
│   ├── au.csv			
│   ├── pretrained
│   └── └── ngp_kf.pth (Corresponds to the trained model ngp_ep00xx.pth)

```
8. Error when preprocessing the digital human with musetalk
```
'MMCV==2.2.0 is used but incompatible'
```
Reduce the pip version to 24.0
```
python -m pip install pip==24.0
```
Then run again
```
mim install "mmdet>=3.1.0" 
mim install "mmpose>=1.1.0"
```

9. Incompatible numpy version
Error: numpy.dtype size changed, may indicate binary incompatibility. Expected 96 from C header, got 88 from PyObject
Solution: Reduce the numpy version
```
pip install --upgrade "numpy==1.26.4"
```

Other references:
https://github.com/lipku/metahuman-stream/issues/43#issuecomment-2008930101 