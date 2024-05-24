# try-on-based-on-flow-and-diffusion
本项目基于GP-VTON的warp模块和DCI-VTON的try-on模块，开展虚拟试衣。

# environment环境配置
```
conda env create -f environment.yaml
conda activate vton
```
# data数据集下载

  本项目基于VITON-HD数据集，在此基础上做了语义分割预处理等改动，数据集格式和[GP-VTON](https://github.com/xiezhy6/GP-VTON/tree/main。)
  将下载的地址放入dataset文件夹中

下载后，dataset文件夹的格式如下：
```
├── dataset
|   ├── test_pairs.txt
|   ├── train_pairs.txt
│   ├── [train | test]
|   |   ├── image
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── cloth
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── cloth-mask
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── cloth-warp
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── cloth-warp-mask
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── unpaired-cloth-warp
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── unpaired-cloth-warp-mask
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
```
# 测试test
## warp服装变形模块
