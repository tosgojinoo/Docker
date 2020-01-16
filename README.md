# Docker

## Dockerhub
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

## Linux 인스톨 과정
+ Package manager repository setup 
+ 패키지 매니저 update
+ 패키지 매니저로 docker install

## Docker CE 설치
+ [Windows] : 윈도우 10 버전에서 하이퍼-V(Hyper-V) 가상머신을 사용해 도커를 실행하려면, 윈도우 10 프로페셔널 이상 운영체제가 설치되어야 함 주의
+ [MacOS, Windows]: .dmg 혹은 .exe 파일 다운로드 후 인스톨러로 Desktop version 설치(추천)
	+ [https://www.docker.com/get-started](www.docker.com/get-started)
+ [MacOS, Windows, linux]: 이후 PowerShell 혹은 Terminal에서 `docker version` 커맨드로 설치 확인
+ [Windows]: 만약 PowerShell이 없다면, ‘Docker toolbox’를 함께 설치하면 Docker client CLI 실행 가능
	+ [https://docs.docker.com/toolbox/toolbox_install_windows/#step-3-verify-your-installation](https://docs.docker.com/toolbox/toolbox_install_windows/#step-3-verify-your-installation)

## Post-installation steps for linux
+ Docker는 커맨드 실행 시 root 권한이 필요
+ 매번 sudo를 붙이는 대신, root 권한 부여


## 이미지 실행
+ 다운로드
pull
+ 컨테이너 생성
creat
+ 이미지 실행 
```bash
(ubuntu 실행시)
$ docker run ubuntu:16.04
(tensorflow 실행시)
$ docker run -d -p 8888:8888 -p 6006:6006 teamlab/pydata-tensorflow:0.1
```


## 도커 명령어
+ -d : detached mode 흔히 말하는 백그라운드 모드
+ -p : 호스트와 컨테이너의 포트를 연결 (포워딩)
+ -v : 호스트와 컨테이너의 디렉토리를 연결 (마운트)
+ -e : 컨테이너 내에서 사용할 환경변수 설정
+ –name : 컨테이너 이름 설정
+ –rm : 프로세스 종료시 컨테이너 자동 제거
+ -it : -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션
+ –link : 컨테이너 연결 [컨테이너명:별칭]
+ exit 실행시, 쉘 & 컨테이너 자동 종료






