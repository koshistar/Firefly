---
title: Animer论文复现
published: 2026-07-02
pinned: false
description: 关于动物的三维重建探索
tags: [科研，学习记录]
category: 科研
draft: true
---

# 实验步骤
## 克隆代码

``` 
git clone https://github.com/luoxue-star/AniMer.git
cd path/to/AniMer
```

## 创建兼容环境
```
conda create -n animer python=3.10 -y
conda activate animer
pip install torch==2.6.0 torchvision==0.21.0 --index-url https://download.pytorch.org/whl/cu126

pip install pytorch-lightning smplx==0.1.28 yacs timm einops
pip install scikit-image trimesh pyrender pyopengl gdown
pip install pydantic==2.10.6 torchmetrics lightning-utilities jsonargparse
pip install pandas opencv-python tqdm open3d
pip install hydra-core hydra-submitit-launcher hydra-colorlog pyrootutils rich
pip install chumpy
```

## 下载数据集

[预训练权重](https://drive.google.com/drive/folders/1_irn6QuYxLEI8PdwqFIdrRZda3w8rpRd)  .\data\AniMer\  
[ViT backbone预训练](https://drive.google.com/drive/folders/1_S6ZZ6HYQ4ixAiN8m5uZxh0hFUsRNDxG)  .\data\pretrained\  
[CtrlAni3D数据集](https://drive.google.com/drive/folders/1_S6ZZ6HYQ4ixAiN8m5uZxh0hFUsRNDxG)  .\data\control_animal3dlatest\  

## 运行Demo

```
pip install hydra-core gdown
pip install gradio
python app.py
```
上传图片后输出ouput图像和3D Mesh

或者使用命令行推理  
```
python demo.py --checkpoint data/AniMer/checkpoints/checkpoint.ckpt --img_folder data/path/images --save_mesh
```
