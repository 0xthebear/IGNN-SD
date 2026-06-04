# IGNN

## Prepare datasets

Crop training dataset DIV2K to sub-images.
```
python ./datasets/prepare_DIV2K_subimages.py
```
Remember to modify the 'input_folder' and 'save_folder' in the above script.

## Dependencies and Installation
The denoising code is tested with Python 3.7, PyTorch 1.1.0 and Cuda 9.0 but is likely to run with newer versions of PyTorch and Cuda.

1 Create conda environment.
```
conda create --name ignn
conda activate ignn
conda install pytorch=1.1.0 torchvision=0.3.0 cudatoolkit=9.0 -c pytorch
```
2 Install PyInn.
```
pip install git+https://github.com/szagoruyko/pyinn.git@master
```
3 Install matmul_cuda.
```
bash install.sh
```
4 Install other dependencies.
```
pip install -r requirements.txt
```

## Training
Use the following command to train the network:

```
python runner.py
        --gpu [gpu_id]\
        --phase 'train'\
        --scale [2/3/4]\
        --dataroot [dataset root]\
        --out [output path]
```
Use the following command to resume training the network:

```
python runner.py 
        --gpu [gpu_id]\
        --phase 'resume'\
        --weights './ckpt/IGNN_x[2/3/4].pth'\
        --scale [2/3/4]\
        --dataroot [dataset root]\
        --out [output path]
```
You can also use the following simple command with different settings in config.py:

```
python runner.py
```

## Testing
Use the following command to test the network on benchmark datasets (w/ GT):
```
python runner.py \
        --gpu [gpu_id]\
        --phase 'test'\
        --weights './ckpt/IGNN_x[2/3/4].pth'\
        --scale [2/3/4]\
        --dataroot [dataset root]\
        --testname [Set5, Set14, BSD100, Urban100, Manga109]\
        --out [output path]
```

Use the following command to test the network on your demo images (w/o GT):
```
python runner.py \
        --gpu [gpu_id]\
        --phase 'test'\
        --weights './ckpt/IGNN_x[2/3/4].pth'\
        --scale [2/3/4]\
        --demopath [test folder path]\
        --testname 'Demo'\
        --out [output path]
```

You can also use the following simple command with different settings in config.py:

```
python runner.py
```

## CodeBase Explanation
[Codebase Explanation (HTML)](./Codebase_Explanation.html)
[Codebase Explanation (MD)](./Codebase_Explanation.md)