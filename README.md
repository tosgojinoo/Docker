# Docker

- [Docker](#docker)
  * [설치 전 (ubuntu)](## '설치 전 (ubuntu)')
  * [tensorflow-gpu 사용 순서](## "tensorflow-gpu 사용 순서")
  * [Nvidia Graphics Driver 설치](##Nvidia Graphics Driver 설치)
  * [Docker CE 설치](#Docker CE 설치)
  * [이미지 실행](#이미지 실행)
    + [예시](#예시)
    + [tensorflow/tensorflow 이미지](#tensorflow/tensorflow 이미지)
  * [Nvidia-docker2, tensorflow-gpu 이미지 설치할 경우](#Nvidia-docker2, tensorflow-gpu 이미지 설치할 경우)
  * [GPU 인식 확인](#GPU 인식 확인)
  * [PyTorch의 경우](#PyTorch의 경우)
  * [도커 시스템 확인](#도커 시스템 확인)
  * [공유 디렉토리 설정](#공유 디렉토리 설정)
    + [(참고) Dockerhub](#(참고) Dockerhub)
  * [(참고) 컨테이너 안에서 충돌 발생시](#(참고) 컨테이너 안에서 충돌 발생시)
  * [(참고) 팁](#(참고) 팁)
  * [(참고) 도커 명령어](#(참고) 도커 명령어)
    + [도커 명령어 옵션](#도커 명령어 옵션)


## 설치 전 (ubuntu)
+ 이전 버전의 도커가 설치되어 있다면, 삭제
```sh
$ sudo apt-get remove docker docker-engine docker.io
```

+ 설치에 필요한 패키지 설치
```sh
# 입력시 '\' 및 줄바꿈 제외
sudo apt-get update && sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
# 또는
sudo apt-get update 
sudo apt-get install apt-transport-https 
sudo apt-get install ca-certificates
sudo apt-get install curl 
sudo apt-get install software-properties-common
```

+ (참고) `apt-get update` 중 아래와 같이 키 에러가 발생한다면, <br>
` W: GPG 오류: http://nginx.org trusty Release: 다음 서명들은 공개키가 없기 때문에 인증할 수 없습니다: NO_PUBKEY ABF5BD827BD9BF62` 

```sh
# '-- recv' 뒤에 오류에서 언급된 PUBKEY 값 입력
$ gpg --keyserver keyserver.ubuntu.com --recv ABF5BD827BD9BF62
$ gpg --export --armor ABF5BD827BD9BF62 | sudo apt-key add -
# 출력 : OK

# 다시 업데이트 실행
$ sudo apt-get update
```

+ (참고) GPG (GNU Privacy Guard ,GnuPG) : 배포 파일의 인증을 확인하는데 사용되는 자유 소프트웨어 패키지. 

+ (참고) apt-transport-https : 패키지 관리자가 https 를 통해 데이터 및 패키지에 접근할 수 있도록 한다.
+ (참고) ca-certificates : ca-certificate 는 certificate authority 에서 발행되는 디지털 서명. SSL 인증서의 PEM 파일이 포함되어 있어 SSL 기반 앱이 SSL 연결이 되어있는지 확인 가능
+ (참고) curl : 특정 웹사이트에서 데이터를 다운로드 받을 때 사용
+ (참고) software-properties-common : PPA 를 추가하거나 제거할 때 사용
+ (참고) PPA (Personal Package Archive) :  개인 패키지 저장소. 개발자가 소스코드를 업로드하면 자동으로 패키지화. 사용자가 다운로드 받아 설치할 수 있게 해주는 소프트웨어 저장소.
+ (참고) wget 대신 굳이 curl 을 쓰는 이유 : 
	+ 둘 다 웹사이트의 데이터를 다운로드 받을 수 있음. 
	+ curl 은 더 다양한 프로토콜 지원. 더 다양한 플랫폼에서 빌드/작동 가능. 자동 압축해제 지원. 업로드와 보내는 방법 지원. 
	+ wget 은 왼손만으로 타이핑 가능. HTTP POST 지원
+ (참고) `|` : 파이프라인, wget/curl 으로 파일을 다운받은 후 셸을 실행한다는 의미
<br><br>

## tensorflow-gpu 사용 순서
+ Nvidia Graphics Driver, Docker, Nvidia-docker2 설치
+ Nvidia GPU, 그리고 Ubuntu OS 가정
<br><br>


## Nvidia Graphics Driver 설치
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
# 설치 확인
$ nvidia-smi
```
<br><br>


## Docker CE 설치
+ [Windows] : 윈도우 10 버전에서 하이퍼-V(Hyper-V) 가상머신을 사용해 도커를 실행하려면, 윈도우 10 프로페셔널 이상 운영체제가 설치되어야 함 주의
+ [MacOS, Windows]: .dmg 혹은 .exe 파일 다운로드 후 인스톨러로 Desktop version 설치(추천)
	+ <https://www.docker.com/get-started>
+ [MacOS, Windows]: 이후 PowerShell 혹은 Terminal에서 `docker version` 커맨드로 설치 확인
+ [Windows]: 만약 PowerShell이 없다면, ‘Docker toolbox’를 함께 설치하면 Docker client CLI 실행 가능
	+ <https://docs.docker.com/toolbox/toolbox_install_windows/#step-3-verify-your-installation>


+ [ubuntu]
```bash
$ sudo apt-get update && sudo apt-get install docker-ce
# Docker는 커맨드 실행 시 root 권한이 필요, 매번 sudo를 붙이는 대신 root 권한 부여
# docker group 생성
$ sudo groupadd docker
# docker group에 사용자 추가
$ sudo usermod -aG docker {USER}
# log out and log back in
```

+ (참고) Linux 인스톨 과정
	+ Package manager repository setup 
	+ 패키지 매니저 update
	+ 패키지 매니저로 docker install
<br><br>



## 이미지 실행
### 예시
```bash
# 포멧
$ docker run [OPTIONS] IMAGE [COMMAND] 

# ubuntu 실행시
$ docker run ubuntu:16.04

# pytorch, StepanKuzmin의 pytorch-notebook
$ docker run -it --rm -p 8888:8888 stepankuzmin/pytorch-notebook

# tensorflow/tensorflow
$ docker run -it -p 8888:8888 tensorflow/tensorflow [command]
$ docker run --runtime=nvidia -it -p 8888:8888 tensorflow/tensorflow:latest-gpu
# Dockerhub의 official tensorflow-gpu 컨테이너 실행 가이드 참고
# Dockerhub -> tensorflow/tensorflow -> tags -> latest-gpu-py3 -> Start GPU (CUDA) container
```


### tensorflow/tensorflow 이미지
```bash
# 포멧
$ docker run -it -p 8888:8888 tensorflow/tensorflow [command]

# 적용
$ nvidia-docker run -it \
--gpus all \
--rm \
--ipc=host \
-p 8888:8888 \
--name deep_jinoo \
-v /home/{$user}/{$폴더}:/tf/{$폴더} \
tensorflow/tensorflow:latest-gpu-py3-jupyter

```
+ (참고) option
	+ -i: interactive, keep stdin open
	+ -t: tty, allocate a terminal
		+ 위 옵션을 주지 않으면 컨테이너 안에서의 터미널을 볼 수 없음
	+ -p: port 8888:8888 
		+ 도커 호스트의 포트 : 컨테이너의 포트
	+ 이미지 이름의 형식 [repo]/[image_name]:[tag_name]
	+ [tag_name]이 주어지지 않을 경우 default는 ‘latest’
 	+ [COMMAND]가 주어지지 않을 경우 tensorflow 이미지의 디폴트 실행 프로그램은
Jupyter notebook

+ (참고) run 명령어 최초 실행 시
	+ 로컬에서 이미지 찾음
	+ 로컬에 없을 경우,  tensorflow/tensorflow 이름을 가진 이미지가 dockerhub에 있는지 찾아 로컬로 가져옴
	+ pull 이후 run 한 것도 동일

+ (참고) `tensorflow/tensorflow:latest` 이미지를 컨테이너로 올리면, 컨테이너에서 Jupyter notebook이 컨테이너의 8888번 포트에서 디폴트로 실행

+ (참고) 현재 터미널의 상태는 컨테이너에 attach 되어 있는 상태
+ (참고) Ctrl + c -> Jupyter 서버 중지 및  자기 할 일을 마친 컨테이너는 바로 정지됨
+ (참고) Ctrl + p, Ctrl + q를 차례대로 입력 -> attach 상태에서 호스트 쉘로 서버를 중지하지 않고 돌아감 (Escape sequence)
+ (참고) 컨테이너의 8888 포트와 로컬의 8888 포트를 이전에 -p 옵션으로 포워딩 해준 상태 -> 로컬 브라우저에서 8888포트로 접속 가능
+ (참고) 콘솔에 보이는 token=[abcdabcd ...] 문자열 복사/붙여넣기
	+ 최초 1회만 확인
	+ 외부에서 접속하는 ip가 바뀌면 다시 입력
	+ 비밀번호 설정 가능

+ (참고) 호스트 쉘에서 attach 명령어, 혹은 `exec -it {container name} /bin/bash` 명령어로   컨테이너 쉘에 접속한 뒤, `jupyter notebook list`를 입력하면 다시 볼 수 있음
+ (참고) `docker attach {container name}`
	+ 컨테이너 생성 시 실행하고 있던 프로세스의 상태 그대로 접속 (jupyter server를 실행시켰을 때의 콘솔 상태로 attach)
	+ ctrl+c를 눌러 확인
<br><br>


## Nvidia-docker2, tensorflow-gpu 이미지 설치할 경우
+ [Nvidia-docker2 설치](https://github.com/NVIDIA/nvidia-docker)
```bash
# nvidia-docker1 설치 되어있을 경우 삭제
# ubuntu
$ docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r docker rm -f
$ sudo apt-get purge -y nvidia-docker

# apt-key에 package repository 추가하고 apt-get update
$ distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
$ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
# ubuntu18.04의 경우 `$distribution`에 `ubuntu18.04`입력됨
# ubuntu16.04의 경우 `$distribution`자리에 `ubuntu16.04`수동 입력
$ curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

# Nvidia-docker2 설치
$ sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
# 시스템 재부팅하여 도커 데몬 재실행
$ sudo systemctl restart docker


# (아래는 과거 버전)
# Nvidia-docker2 설치
$ sudo apt-get install -y nvidia-docker2
$ sudo pkill -SIGHUP dockerd
# (/아래)


# Nvidia 설치 확인용 컨테이너 실행
# (자동으로 CUDA 이미지가 다운로드 받아지는데 나중에 지워도 무방함)
#### CUDA Toolkit 9.0 버전의 'nvidia/cuda:9.0-base' 컨테이너 이미지 다운, 'nvidia-smi' 명령어를 컨테이어 안에서 실행
$ docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
('--runtime=nvidia' 옵션 : Host의 Nvidia 드라이버가 컨테이너에서도 잘 적용될 수 있음을 직업 확인)
#### Test nvidia-smi with the latest official CUDA image
$ docker run --gpus all nvidia/cuda:9.0-base nvidia-smi

# Start a GPU enabled container on two GPUs
$ docker run --gpus 2 nvidia/cuda:9.0-base nvidia-smi

# Starting a GPU enabled container on specific GPUs
$ docker run --gpus '"device=1,2"' nvidia/cuda:9.0-base nvidia-smi
$ docker run --gpus '"device=UUID-ABCDEF,1"' nvidia/cuda:9.0-base nvidia-smi

# Specifying a capability (graphics, compute, ...) for my container
# Note this is rarely if ever used this way
$ docker run --gpus all,capabilities=utility nvidia/cuda:9.0-base nvidia-smi
```
<br><br>

## GPU 인식 확인 
+ 실행된 GPU 컨테이너의 jupyter notebook 안에서 GPU 를 잘 인식하는지 확인
```python
from tensorflow.python.client import device_lib
def get_available_gpus():
	return [x.name for x in device_lib.list_local_devices()]

get_available_gpus()
# 출력 ['/device:GPU:0', '/device:XLA_GPU:0']
```
<br><br>


## PyTorch의 경우
+ 노트북 cell 또는 bash에서 파이토치 설치
`!pip install torch torchvision`
+ 또는, 컨테이너 쉘에 새로 접속 하여 설치
`$ docker exec -it {container_name} /bin/bash`
 + 이미지 태크가 'py3'로 명시되어 있어도 'pip'로 설치, 'pip3' X
+ 실행된 gpu 컨테이너의 jupyter notebook 안에서 gpu를 잘 인식하는지 확인
```python
import torch
torch.cuda.get_device_name(0)
(출력) 'GeForce GTX 1080 Ti'
```
<br><br>


## 도커 시스템 확인
```bash
$ sudo systemctl status docker
```
+ `systemctl` : 리눅스에서 서비스를 등록, 삭제(mask, unmask) / 활성화, 비활성화(enable, disable) / 시작, 중지, 재시작(start, stop, restart) / 상태 확인(status) / 서비스 확인(list-units, list-unit-files)을 할 수 있는 명령어
<br><br>


## 공유 디렉토리 설정
+ 컨테이너 실행 종료 후 따로 설정을 해 주지 않으면 기본적으로 자신의 자취를 Host OS에 남기지 않음(휘발성)
+ 작업/저장 파일들의 호스트 직접 접근 불가
+ 컨테이너가 내려간 상태에서 삭제되면 같이 삭제
+ 때문에, Host OS <=> 컨테이너 간  공유 디렉토리 생성 관리
+ 최초 컨테이너 run 시에,  
	+ volume option인 `-v`를 사용, 또는
	+ 마운트 옵션인 `—mount`, `—bind` 등을 사용해 해결
+ docker volume
	+ 호스트의 파일시스템과 격리된 도커 컨테이너의 파일시스템을 이어주는 역할
	+ docker run 커맨드 실행시 `-v` 옵션으로  임시로 생성, 또는
	+ volume만 따로 생성하고 run 시에 붙여서  실행
	+ `—mount`, `—bind` 또한 비슷한 개념의 옵션
+ 적용 :
	+ `${HOME}/code` : host path
	+ `/notebooks ` : container path
```bash
# 이전
$ docker run -it -p [host port]:[container port] -v ["$/{HOME}/code", host path]:["/notebooks ", container path] [image_id] [command]
# 적용
$ docker run -it -p 8888:8888 -v /Users/내폴더:/notebooks  tensorflow/tensorflow
# commit 을 통해 이미지 업데이트
$ docker commit [container_id] [image_id]:[tag]
```
<br><br>

## 컨테이터로의 파일/폴더 복사
docker cp (이동할 폴더의 절대경로)/. (컨테이너 name):(컨테이너 내 파일들을 받을 폴더의 절대 경로)



### (참고) Dockerhub
+ Docker image 보관
+ (Public repository) tensorflow/tensoflow 다운, 활용
	+ cpu 버전 : tensorflow/tensorflow:latest, (tab)
	+ gpu 버전 : (Nvidia) tensorflow/tensorflow:latest-gpu
	+ PyTorch 활용 : TF 컨테이너 위에 pip로 pytorch 설치 후 이미지에 변경사항 커밋
+ Nvidia-docker 프로젝트는 윈도우 지원 않함
	+ tensorflow-gpu 설치 불가
	+ cpu 버전 설치 가능
+ 사용시 호스트 OS(Ubonto or Linux)에는 아래 내용만 설치
	+ gpu 경우 : 그래픽카드와 맞는 버전의 Nvidia-driver, docker, nvidia-docker2
	+ cpu 경우 : docker
<br><br>


## (참고) 컨테이너 안에서 충돌 발생시
+ Tensorflow 컨테이너는 root권한으로 모든 파이썬 패키지가 설치
+ docker exec 명령어로 컨테이너 쉘에 접속한 뒤, pip으로만 패키지를 설치/삭제
+ 안될경우, 해당 컨테이너를 내리고 삭제한 뒤 기존에 커밋했었던 다른 이미지로 다시 컨테이너 띄우기
+ 안될경우, tensorflow 버전이 문제 -> dockerhub에 있는 tensorflow 이미지의  다른 tag로 교체
<br><br>


## (참고) 팁
+ 많이 쓰는 docker 명령어들은 alias, 혹은 스크립트를 짜서 로컬에 놓으면 좀 더 편하게 사용 가능
+ Tensorboard 실행을 염두할 경우, 처음에 컨테이너의 포트를 하나 더 열어주시는 것 좋음 (docker run -it -p 8888:8888 -p 8889:6006 tf/tf:latest) 
+ 노출될 포트 설정를 포함한 ‘run’의 옵션들은 이미지로부터 컨테이너가 생성될 때만 설정할 수 있고, 컨테이너로 올린 이후에 변경은 힘듭니다. 
 + 패키지를 다 깔았는데 포트 오픈을 깜빡했다면? -> 변경사항을 이미지에 커밋 후 다시 run
<br><br>


## (참고) 도커 명령어
+ 실행중인 컨테이너 목록<br>
`$ docker ps -a`

+ 이미지 -> 컨테이너 실행<br>
`$ docker run`

+ 컨테이너 중지<br>
`$ docker stop {container ID}`

+ 컨테이너 중지 -> 시작<br>
`$ docker start {container ID}`

+ 컨테이너 중지 -> 제거<br>
`$ docker rm {container ID#1} {container ID#2}`

+ 컨테이너 생성<br>
`$ docker creat`

+ 로컬에 저장된 이미지들 목록<br>
`$ docker images`

+ 내 로컬에 있는 이미지를 원격 레포지토리로 보냄<br>
`$ docker push`

+ 원격 레포지토리의 이미지를 로컬로 당겨옴<br>
`$ docker pull`

+ 이미지 삭제<br>
`$ docker rmi {image ID}`

+ 컨테이너 로그<br>
`$ docker logs --tail 10 {container ID}`

+ 실시간 컨테이너 로그<br>
`$ docker logs -f {container ID}`

+ 컨테이너 안에서 프로세스가 실행되고 있는 그 상태의 쉘로 접속<br>
`$ docker attach {container ID}` 

+ 컨테이너 안의 변경사항들을 이미지에 반영(커밋)<br>
`$ docker commit {container ID} {image ID}:{tag}`

+ 실행중인 컨테이너에 명령 실행, 내부 쉘 접속 (run은 새로 컨테이너를 만들어 실행, exec는 실행 중인 컨테이너에 명령 실행)<br>
```
$ docker exec [option] {container ID} command [arg ...]
$ (예) docker exec -it mysql /bin/bash
```

+ 도커 컨테이너 업데이트 : 업데이트시 컨테이너에 생성된 파일은 모두 삭제될 수 있음. 유지해야 할 데이터는 컨테이너 내부가 아닌 외부 스토리지에 저장.<br>

+ 도커 컴포즈 : 도커 컴포즈 툴을 이용해, 도커 명령을 하나의 설정으로 간편하게 처리 가능<br>
[Docker Compose](https://docs.docker.com/compose/)

+ exit 실행시, 쉘 & 컨테이너 자동 종료

### 도커 명령어 옵션
+ -d : detached mode 흔히 말하는 백그라운드 모드
+ -p : 호스트와 컨테이너의 포트를 연결 (포워딩)
+ -v : 호스트와 컨테이너의 디렉토리를 연결 (마운트)
+ -e : 컨테이너 내에서 사용할 환경변수 설정
+ –name : 컨테이너 이름 설정
+ –rm : 프로세스 종료시 컨테이너 자동 제거
+ -it : -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션
+ –link : 컨테이너 연결 [컨테이너명:별칭]





