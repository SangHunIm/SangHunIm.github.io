---
layout: post
title:  "ubuntu 18.04 Setup for Deep Learning"
subtitle:   "ubuntu 18.04 Setup for Deep Learning"
categories: ubuntu
tags: ubuntu
comments: true
---

- ubuntu 18.04 설치 및 DL(w. pytorch)을 위한 기본 프로그램 설치에 대한 글입니다.

---


## ubuntu 18.04 설치
새로 서버가 들어와 관리 편의성 및 pytorch 사용을 위해 재설치 하며 남긴 기록입니다.

#### HW spec
- AMD treadreaper
- Titan RTX * 4

#### 설치할 소프트웨어
- ubuntu 18.04 desktop
- ssh
- ftp
- nvidia driver

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

```{.bash}
sudo fdisk -l
```

명령어를 통해 현재 연결된 디스크의 정보를 확인

```{.bash}
sudo blkid
```

명령어를 통해 마운트 할 디스크의 UUID를 기록/복사

```{.bash}
sudo nano /etc/fstab
```

명령어로 fstab에 하단 코드 입력

마운트 위치는 미리 생성해 두어야함

```{.bash}
UUID=[기록해둔 UUDI]    [마운트 위치]   [디스크 포맷]   defaults    0   0
# 예를 들어 UUID=asdfsadfasfasdfasdf   /data   ext4    defaults    0   0
```

fstab에 오타가 발생하면 reboot시 서버에서 직접 fstab을 수정해야합니다.

따라서 reboot전 꼭 다음 명령어를 실행 해야합니다.

명령어 실행시 reboot 없이도 마운트가 되며 에러 발생시 에러 메세지가 출력됩니다.

```{.bash}
sudo mount -a
```

#### NAS 연결

NAS 마운트를 위해 cifs-utils를 설치해야합니다.

```{.bash}
sudo apt-get install cifs-utils
```

이후 HDD, SSD 마운트와 같이 fstab에 다음 내용을 추가합니다.

```{.bash}
//[nas 주소]/[마운트할 폴더]       [마운트 위치]       cifs    user=[nas 유저],pass=[nas 유저 비밀번호],uid=[마운트 위치 소유자 설정],gid=[마운트 위치 그룹 설정],rw,file_mode=0775,dir_mode=0775
# 예를 들어 //192.168.0.1/data       /data/nas       cifs    user=admin,pass=admin11,uid=0,gid=0,rw,file_mode=0775,dir_mode=0775
```

---

### ssh/ftp 설치
#### ssh 설치

```{.bash}
sudo apt-get install openssh-server
sudo systemctl start sshd
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

현재 pytorch(1.4.0)은 자체적인 cuda/nccl/cudnn lib을 사용하므로 nvidia driver만 설치하면 됩니다.

따라서 최신버전의 nvidia driver을 설치합니다.

#### Nvidia Driver

```{.bash}
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo ubuntu-drivers autoinstall
sudo reboot
```

재시작이 되면 다음 명령어 실행하여 동작 확인

```{.bash}
nvidia-smi
```

---

