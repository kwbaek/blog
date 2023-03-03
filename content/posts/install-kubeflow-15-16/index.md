---
title: "Install Kubeflow 15 16"
date: 2023-02-09T16:11:40+09:00
draft: false
categories: "os/linux"
tags:
- "os"
- "linux"
  comments: true
---기

# kubeflow 16 설치하기 

## 개요
- kubeflow 를 설치 시 쉽고 빠른 좋은 설치 방법이 있어 공유 합니다.
- 하기 참조 링크를 참고하여 kubeflow 설치 및 실행 방법에 대해 정리 한 문서 입니다.


## 설치전 기존 설치환경 초기화

### kubernetes 초기화
- 기존에 쿠버네티스가 설치되어 있으면 기존 쿠버네티스를 삭제 및 초기화 하여 새로운 설치를 시도 합니다.

### 실행 스크립트
```shell
## 쿠버네티스 초기화
sudo kubeadm reset
y

## 쿠버네티스 && 도커 기동 중지
sudo systemctl stop kubelet
sudo systemctl stop docker

## 쿠버네티스 네트워크 설정( Cluster Network Interface ) 삭제
sudo ip link delete cni0
sudo ip link delete flannel.1

## 쿠버네티스 관련 파일 삭제
sudo rm -rf /var/lib/cni/
sudo rm -rf /var/lib/kubelet/*
sudo rm -rf /var/lib/etcd
sudo rm -rf /run/flannel
sudo rm -rf /etc/cni
sudo rm -rf /etc/kubernetes
sudo rm -rf ~/.kube

## 쿠버네티스 관련 패키지 삭제(Ubuntu)
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube* -y
sudo apt-get autoremove
```

### docker 초기화

### 실행 스크립트
```shell
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras

sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd

sudo apt-get purge nvidia-docker2

sudo apt-get purge nvidia-container-toolkit
sudo apt autoremove
```

## kubeflow 설치

## kubeflow 설치 스크립트 clone 
```shell
git clone https://github.com/myoh0623/kubeflow.git
```

## 1. install cudnn

### 실행 스크립트

```shell
cd kubeflow_tutorial/kubeflow/section1_install
./1_install_cudnn.sh
```

테스트 서버에 gpu 는 A100 이고 스크립트 에 있는 cuda 버전 이 낮아 최신 버전으로 변경하기 위해 다음과 같이 수정 


1_install_cudnn.sh
```shell
#!/bin/bash
sudo apt-get -y update
sudo apt-get -y remove --purge '^nvidia-.*'
sudo apt-get -y remove --purge 'cuda-.*'
sudo apt-get -y install nvidia-cuda-tookit
sudo apt-get -y install nvidia-cuda-toolkit
nvcc -V
whereis cuda
mkdir ~/nvidia
cd ~/nvidia
#CUDNN_TAR_FILE="cudnn-10.1-linux-x64-v7.6.5.32.tgz"
#wget https://developer.download.nvidia.com/compute/redist/cudnn/v7.6.5/${CUDNN_TAR_FILE}
#tar -xzvf ${CUDNN_TAR_FILE}




CUDNN_TAR_FILE="cudnn-linux-x86_64-8.7.0.84_cuda11-archive.tar.xz"
wget https://developer.download.nvidia.com/compute/redist/cudnn/v8.7.0/local_installers/11.8/${CUDNN_TAR_FILE}
tar -xvf ${CUDNN_TAR_FILE}

sudo cp cuda/include/cudnn.h /usr/lib/cuda/include/
sudo cp cuda/lib64/libcudnn* /usr/lib/cuda/lib64/
sudo chmod a+r /usr/lib/cuda/lib64/libcudnn*
sudo chmod a+r /usr/lib/cuda/include/cudnn.h
echo "export LD_LIBRARY_PATH=/usr/lib/cuda/lib64:$LD_LIBRARY_PATH" >> ~/.bashrc
export "LD_LIBRARY_PATH=/usr/lib/cuda/include:$LD_LIBRARY_PATH" >> ~/.bashrc
source ~/.bashrc
ubuntu-drivers devices
sudo apt install -y nvidia-driver-515
echo "reboot....."


```

### 실행결과


## 2. install docker

### 실행 스크립트
```shell
./2_install_docker.sh
```

### 실행결과

## 3. install nvidia docker

### 실행 스크립트
```shell
./3_install_nvidia_docker.sh
```

docker 오류발생으로 다음과 같이 스크립트 수정
3_install_nvidia_docker.sh
```shell
#!/bin/bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get -y update

sudo apt-get install -y nvidia-container-toolkit
sudo nvidia-ctk runtime configure --runtime=docker --set-as-default
#sudo apt-get -y install nvidia-docker2

sudo systemctl restart docker
sudo docker run --rm --runtime=nvidia --gpus all nvidia/cuda:11.6.2-base-ubuntu20.04 nvidia-smi
#sudo docker run --runtime nvidia nvidia/cuda:10.1-base /usr/bin/nvidia-smi

#sudo bash -c 'cat <<EOF > /etc/docker/daemon.json
#{
#    "default-runtime": "nvidia",
#    "runtimes": {
#        "nvidia": {
#            "path": "/usr/bin/nvidia-container-runtime",
#            "runtimeArgs": []
#        }
#    }
#}

#{
#  "exec-opts": ["native.cgroupdriver=systemd"],
#  "log-driver": "json-file",
#  "log-opts": {
#    "max-size": "100m"
#  },
#  "storage-driver": "overlay2",
#  "storage-opts": [
#    "overlay2.override_kernel_check=true"
#  ]
#}
#EOF'



sudo bash -c 'cat <<EOF > /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF'

sudo systemctl restart docker


```

### 실행결과

## 참조
- Kubeflow Essentials: https://youtube.com/playlist?list=PL6ZWs3MJaiphOwtHQvBCA4GNw-EPDely-
- 모두의 MLOps : https://mlops-for-all.github.io/