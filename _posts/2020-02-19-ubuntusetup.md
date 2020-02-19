---
layout: post
title:  "ubuntu 18.04 Setup for Deep Learning"
subtitle:   "ubuntu 18.04 Setup for Deep Learning"
categories: ubuntu
tags: ubuntu
comments: true
---

- ubuntu 18.04 설치 및 DL을 위한 기본 프로그램 설치에 대한 글입니다.

---


## ubuntu 18.04 설치
새로 서버가 들어와 관리 편의성 및 nccl을 통한 분산 컴퓨팅을 위해 재설치 하며 남긴 기록입니다.

#### HW spec
- AMD treadreaper
- Titan RTX * 4

#### 설치할 소프트웨어
- ubuntu 18.04 desktop
- ssh
- ftp
- nvidia driver
- cuda
- cudnn
- nccl

---

### ubuntu 18.04 설치
1. iso 이미지 다운로드
2. usb에 이미지 설치 (rufus 사용)
3. bios 설정을 통해 usb로 부팅
4. ubuntu 설치

---

### ubuntu 기본 설정
설치 후 
```{.bash}
sudo apt-get update
sudo apt-get upgrade
```
실행

#### Static IP 설정
gui 환경에서 설정

#### HDD, SSD 마운트
- 추후 기술 예정

#### NAS 연결
- 추후 기술 예정

---

### ssh/ftp 설치
#### ssh 설치
```{.bash}
sudo apt-get install openssh-server
sudo systemctl start sshd
sudo reboot
```
#### ftp 설치

```{.bash}
sudo apt-get install vsftpd
sudo nano /etc/vsftpd.conf
```

설정파일의 다음 항목 수정
```{.bash}
write_enable=YES #ftp를 이용한 쓰기 허용
local_umask=022 #ftp를 이용해 전송한 파일의 권한 설정 (777-022=755)
```

설정 이후 다음 명령어
```{.bash}
sudo systemctl restart vsftpd
```

---

### GPU Setup
현재 pytorch는 cuda 10.1까지 지원

따라서 nvidia driver=418, cuda=10.1 설치

#### Nvidia Driver
```{.bash}
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt-get install nvidia-driver-418
sudo reboot
```

재시작이 되면 다음 명령어 실행하여 동작 확인
```{.bash}
nvidia-smi
```

#### CUDA
nvidia 공식홈페이지 cuda 10.1 설치법을 참고하여 작성하였습니다.

```{.bash}
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget http://developer.download.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda-repo-ubuntu1804-10-1-local-10.1.243-418.87.00_1.0-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1804-10-1-local-10.1.243-418.87.00_1.0-1_amd64.deb
sudo apt-key add /var/cuda-repo-10-1-local-10.1.243-418.87.00/7fa2af80.pub
sudo apt-get update
sudo apt-get -y install cuda
```

#### cuDNN
cuDNN 공식 홈페이지 가입 후 해당 버전에 맞는 cuDNN 다운로드 후 다음 코드 실행

```{.bash}
sudo tar -zxvf [cudnn파일]
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
sudo apt-get install libcupti-dev
```

설치 이후 환경변수를 추가해줘야 합니다.

```{.bash}
nano ~/.bashrc
```

하단에 다음 라인 추가
```{.bash}
export PATH=/usr/local/cuda-10.1/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-10.1/lib64\${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

설치 확인법
```{.bash}
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
```

#### nccl

---

### 개인별 설정
#### 환경변수 설정
#### anaconda 설치

