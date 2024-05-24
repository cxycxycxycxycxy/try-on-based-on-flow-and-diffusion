# try-on-based-on-flow-and-diffusion
本项目基于GP-VTON的warp模块和DCI-VTON的try-on模块，开展虚拟试衣。

# environment环境配置
```
conda env create -f environment.yaml
conda activate vton
```
# data数据集下载

  本项目基于VITON-HD数据集，在此基础上做了语义分割预处理等改动，数据集格式和[GP-VTON](https://github.com/xiezhy6/GP-VTON/tree/main。).
  将下载的地址放入dataset文件夹中

下载后，dataset文件夹的格式如下：
```
├── dataset
|   ├── test_pairs.txt
|   ├── train_pairs.txt
│   ├── [train | test]
|   |   ├── agnostic-v3.2
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── cloth
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── cloth_mask-bytedance
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── cloth_parse-bytedance
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
|   |   ├── image
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
|   |   ├── openpose_img
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
|   |   ├── openpose_json
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
|   |   ├── parse-bytedance
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]

```
# 测试test  
## warp服装变形模块  
```
cd warp
python3 test_warping.py \
    --name test_partflow_vitonhd_unpaired_1109 \
    --PBAFN_warp_checkpoint 'checkpoints/gp-vton_partflow_vitonhd_usepreservemask_lrarms_1027/PBAFN_warp_epoch_121.pth' \
    --resize_or_crop None --verbose --tf_log \
    --dataset vitonhd --resolution 512 \
    --batchSize 2 --num_gpus 1 --label_nc 14 --launcher pytorch \
    --dataroot /DATASET_PATH/ \
    --image_pairs_txt test_pairs.txt
```
变形后的服装保存在dataset的test的路径下，格式为：
```
│   │   ├── cloth-warp
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── cloth-warp-mask
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── unpaired-cloth-warp
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
│   │   ├── unpaired-cloth-warp-mask
│   │   │   ├── [000006_00.jpg | 000008_00.jpg | ...]
```
## try-on试衣合成模块
```
cd tryon
python test.py --plms --gpu_id 0 \
--ddim_steps 100 \
--outdir results/viton \
--config configs/viton512.yaml \
--ckpt /CHECKPOINT_PATH/viton512.ckpt \
--dataroot /DATASET_PATH/ \
--n_samples 8 \
--seed 23 \
--scale 1 \
--H 512 \
--W 512 \
--unpaired
```

# 训练train
## warp服装变形模块
```
cd warp
python3 -m torch.distributed.launch --nproc_per_node=8 --master_port=7129 train_warping.py \
    --name gp-vton_partflow_vitonhd_usepreservemask_lrarms_1027 \
    --resize_or_crop None --verbose --tf_log \
    --dataset vitonhd --resolution 512 \
    --batchSize 2 --num_gpus 8 --label_nc 14 --launcher pytorch  \
    --dataroot /DATASET_PATH/ \
    --image_pairs_txt train_pairs_1018.txt \
    --display_freq 320 --print_freq 160 --save_epoch_freq 10 --write_loss_frep 320 \
    --niter_decay 50 --niter 70  --mask_epoch 70 \
    --lr 0.00005
```
## tryon试衣合成模块
```
cd tryon
python -u main.py \
--logdir models/dci-vton \
--pretrained_model checkpoints/model.ckpt \
--base configs/viton512.yaml \
--scale_lr False
```
