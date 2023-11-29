# Guide for CTransformers library usage on server 190

Since the server 190 has CUDA 11 installed, while the ctransformers library wants CUDA 12, here you can find a guide on how to get it to work anyway.

First thing first, try typing:

```bash
nvcc --version
```
If the answer is like 

```bash
Command 'nvcc' not found, but can be installed with:

apt install nvidia-cuda-toolkit
Please ask your administrator
```

then type:
```bash
nano .bashrc
```

and add this lines at the end of the file

```bash
export CUDA_HOME=/usr/local/cuda
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64
export PATH=$PATH:$CUDA_HOME/bin
```

save the file and run:

```bash
source ~/.bashrc
```

Then try again:

```bash
nvcc --version
```

You should get an answer like this:

```bash
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2022 NVIDIA Corporation
Built on Wed_Jun__8_16:49:14_PDT_2022
Cuda compilation tools, release 11.7, V11.7.99
Build cuda_11.7.r11.7/compiler.31442593_0
```

No follow this github [comment](https://github.com/marella/ctransformers/issues/90#issuecomment-1734127938) to compile ctransformers library for CUDA 11.