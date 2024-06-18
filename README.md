## 环境配置
```
#配置torch和torchvision
conda install pytorch==1.13.1 torchvision==0.14.1 torchaudio==0.13.1 pytorch-cuda=11.7 -c pytorch -c nvidia
#配置ByteTrack所需环境
pip3 install -r requirements.txt 
python3 setup.py develop 
pip3 install cython pycocotools cython_bbox 
#配置MixFormer所需环境
sudo apt-get install libturbojpeg 
pip3 install -r MixViT/requirements.txt
```
## 数据集准备
下载 [SportsMOT](https://github.com/MCG-NJU/SportsMOT), ，按以下目录存放 `<NB_HOME>/datasets` ：
```
datasets
   |——————SportsMOT
           └——————train
           └——————val
           └——————test
```
将数据集转化为 COCO标签格式，创建TrackingNet格式的注释文件:
```
cd <MixSort_HOME>
python3 tools/convert_sportsmot_to_coco.py
python3 tools/convert_sportsmot_anno.py
```
## 跟踪任务
```
cd <MixSort_HOME>
# track on SportsMOT
python3 tools/track_mixsort.py -expn {exp_name} -f exps/example/mot/yolox_x_sportsmot.py -c pretrained/yolox_x_sports_train.pth.tar -b 1 -d 1 --config track
```

## 结果评估
将需要评估的数据存放到`TrackEval/data`目录下。例如ground truth存放至 `TrackEval/data/gt/mot_challenge/sports-val`，
将跟踪结果放至 `TrackEval/data/trackers/mot_challenge/sports-val/<exp_name>/data`：
```
data
├── gt
│   └── mot_challenge
│       └── sports-val
│       └── ... (other datasets)
│       └── seqmaps
│           └── sports-val.txt
│           └── ... (other datasets)
└── trackers
    └── mot_challenge
        ├── sports-val
           ├── <exp_name>
           │   └── data
           │       └── <seq_name>.txt
           │       └── ... (other sequences)
           └── ... (other exps)
           
```
运行以下命令评估跟踪结果：
```
cd <MixSort_HOME>
python TrackEval/scripts/run_mot_challenge.py --BENCHMARK sports --SPLIT_TO_EVAL val --TRACKERS_TO_EVAL <exp_name>
```
