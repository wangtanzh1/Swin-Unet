# Swin-Unet
This repository contains the official implementation of the paper: "A Transformer-Based Network for Improving the Quality of 2D Cine Magnetic Resonance Images Using 3D Prior Knowledge".
This framework utilizes a hybrid architecture combining Swin Transformer blocks and U-Net to synthesize high-quality localization MR (sMR) from low-quality cine-MR sequences.
1. Requirements
The code has been tested on Windows 10 with Python 3.7 and PyTorch 1.12.1. To run the code, please install the following dependencies:
·  Python == 3.7
·  PyTorch == 1.12.1+cu113
·  Torchvision == 0.13.1+cu113
·  numpy
·  SimpleITK
·  nibabel
·  timm
·  einops
You can install the dependencies using the following command:
pip install torch==1.12.1+cu113 torchvision==0.13.1+cu113 --extra-index-url https://download.pytorch.org/whl/cu113
pip install numpy SimpleITK nibabel timm einops
2. Dataset Preparation
Please organize the dataset as follows. The project expects paired Cine-MR (input) and Reference-MR (ground truth) slices.
Directory Structure:
Dataset/
├── MR2cine/
│   ├── MR/           # Ground Truth (Reference MR slices)
│   ├── train_val/        # Cine-MR slices for training and validation
│   └── test/         # Cine-MR slices for testing
·  MR: Contains all aligned high-quality localization MR images.
·  train: Contains cine-MR images used for training and validation.
·  test: Contains cine-MR images for final evaluation.
3. Training
To train the Swin-ResViT model, run train.py. Note on Reproducibility: We set a fixed random seed (--seed 777) to ensure the reproducibility of the data splitting and training process.
Basic Usage:
python train.py --train_img_dir ./dataset/MR2cine/train \
                --mr_img_dir ./dataset/MR2cine/MR \
                --batch_size 24 \
                --n_epochs 150 \
                --gpu_id 0
Hyperparameters:
The default hyperparameters are set to match the experiments in the paper:
·  Loss Weights: The model balances pixel-wise accuracy and structural similarity using the following weights (consistent with the manuscript):
·  --lambda_MAE 3 (MAE Loss)
	·  --lambda_ssim 1 (SSIM Loss)
	·  --lambda_grad 2 (Gradient Loss)
·  Optimizer: Adam (lr=0.0004, beta1=0.5, beta2=0.999)
4. Inference / Testing
To evaluate the model on the test set using the pre-trained weights:
python test.py --test_img_dir ./dataset/MR2cine/test \
               --checkpoint_dir ./expr/checkpoints \
               --load_epoch 150
5. Files Description
·  train.py: Main script for training the Swin-ResViT model.
·  test.py: Script for inference and metric evaluation.
·  models/: Contains the Swin-ResViT network architecture definitions.
·  utils/: Contains data loaders and helper functions.
·  loss.py: Implementation of the combined loss function (MAE + SSIM + Gradient).
