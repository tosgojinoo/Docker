# [Setting] Nvidia & Cuda

## 설치 여부 및 버전 확인
### Nvidia Driver
```
$ cat /proc/driver/nvidia/version
(or)
$ nvidia-smi
```

### CUDA
```
$ nvcc --version
```

### CUDNN
```
$ cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
```
<br><br>

## Nvidia Graphics Driver 설치
### 간단한 방법
```bash
# 설치된 gpu에 맞는 드라이버 확인
$ ubuntu-drivers devices
# recommended driver 바로 설치
$ sudo ubuntu-drivers autoinstall

# 또는, ppa 추가 후 설치
$ sudo add-apt-repository ppa:graphics-drivers/ppa # ppa 추가
$ sudo apt update
$ sudo apt install nvidia-440 #(버전)
# 재부팅
```


### Ubuntu 16.04 (CUDA 10) + cudnn + driver 설치
```bash
# NVIDIA package repositories 추가
# HTTPS support for apt-key 추가
$ sudo apt-get install gnupg-curl
$ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
$ sudo dpkg -i cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
$ sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
$ sudo apt-get update wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
$ sudo apt install ./nvidia-machine-learning-repo-ubuntu1604_1.0.0-1_amd64.deb
$ sudo apt-get update

# NVIDIA driver 설치
# Issue with driver install requires creating /usr/lib/nvidia
$ sudo mkdir /usr/lib/nvidia
# '--no-install-recommends' : 최소설치
$ sudo apt-get install --no-install-recommends nvidia-driver-418
# 재부팅 후 nvidia-smi 확인

# Install development and runtime libraries (~4GB)
$ sudo apt-get install --no-install-recommends \
    cuda-10-0 \
    libcudnn7=7.6.2.24-1+cuda10.0  \
    libcudnn7-dev=7.6.2.24-1+cuda10.0

# Install TensorRT. Requires that libcudnn7 is installed above.
sudo apt-get install -y --no-install-recommends libnvinfer5=5.1.5-1+cuda10.0 \
    libnvinfer-dev=5.1.5-1+cuda10.0
```
<br><br>

## 삭제
### Ndivia driver 삭제
```
$ sudo apt-get remove --purge '^nvidia-.*' 
``` 


### CUDA 삭제
```
$ sudo apt-get --purge remove 'cuda*'
sudo apt-get autoremove --purge 'cuda*'
```


### CUDA 파일 삭제
```
$ sudo rm -rf /usr/local/cuda
(or)
$ sudo rm -rf /usr/local/cuda-10.0
```

