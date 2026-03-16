# TrafficMoE: Heterogeneity-aware Mixture of Experts for Encrypted Traffic Classification

[![Pytorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=flat-square&logo=pytorch&logoColor=white)](https://pytorch.org/)


This is the official PyTorch implementation for the paper: **TrafficMoE: Heterogeneity-aware Mixture of Experts for Encrypted Traffic Classification**.


Create a virtual environment and install the required packages:
```bash
conda create -n trafficmoe python=3.9 -y
conda activate trafficmoe
pip install torch torchvision torchaudio
pip install timm tensorboard numpy
```
📂 Data Preparation
```bash
## 📂 Data Preparation

The dataset used in this project is publicly available. You can download the compressed dataset  from the following links:


* [Baidu Netdisk (百度网盘)](https://pan.baidu.com/s/1m3Wl_1sz1ihnaE9dLiX1CQ?pwd=y3xz) (Extraction code / 提取码: `y3xz`)

After downloading the dataset, extract it into the `ddos_datasets/flow_image/` directory. The folder structure should look exactly like this:

```text

ddos_datasets/dataset/
├── train/
│   ├── class_0/
│   │   ├── sample1.npy
│   │   └── sample2.npy
│   └── class_1/
├── valid/
│   └── ...
└── test/
    └── ...
```

🚀 Training Pipeline
The training process of TrafficMoE consists of two stages: Pre-training and Fine-tuning.

Stage 1: Pre-training
In this stage, the model is trained in a self-supervised manner to reconstruct masked packet patches.

```bash
python main.py \
    --model traffic_moe_pretrain \
    --data_path ./ddos_datasets/flow_image \
    --output_dir ./output/pretrain \
    --steps 150000 \
    --batch_size 128 \
    --lr 1e-3 \
    --warmup_epochs 20 \
    --weight_decay 0.05 \
 ```
Stage 2: Fine-tuning
After pre-training, fine-tune the model with a cslassification head on your specific traffic dataset. We load the pre-trained weights using the --finetune argument.


```bash
python main.py \
    --model traffic_moe_classifier \
    --finetune ./output/pretrain/checkpoint-best.pth \
    --data_path ./ddos_datasets/flow_image \
    --output_dir ./output/finetune \
    --epochs 120 \
    --batch_size 64 \
    --lr 2e-3 \
    --warmup_epochs 5 \
    --nb_classes 7 \
    --device cuda
 ```
