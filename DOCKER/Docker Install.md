# 리눅스

리눅스에 Docker를 설치하는 방법은 두 가지가 있다.

1. [Docker에서 제공하는 자동 설치 스크립트를 이용하는 방법](#자동-설치-스크립트)
2. [리눅스 배포판의 패키지 시스템을 이용하여 직접 설치하는 방법](#직접-설치)

## 자동 설치 스크립트

Docker는 리눅스 배포판의 종류를 자동으로 인식하여 Docker 패키지를 설치해주는 스크립트를 제공한다.

```wget
$ sudo wget -q0- https://get.docker.com/ | sh
```

```curl
$ sudo curl -fsSL https://get.docker.com | sh
```

## 직접 설치

> 우분투

우분투에서 패키지로 직접 설치하는 방법이다.

우분투 22.04 LTS 64비트 기준

```
$ sudo apt update
$ sudo apt install -y \
	ca-certificates \
	curl \
	gnupg \
	lsb-relesase
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
	sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=etc/apt/keyrings/docker.gpg] \
	https://download.docker.com/linux/ubuntu \
	$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
$ sudo apt update
$ sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

> REHL, CentOS, Amazon Linux

RHEL, CentOS, Amazon Linux에서 패키지로 직접 설치하는 방법이다.

```RHEL
$ sudo yum install -y yum-utils
$ sudo yum-config-manager \
	--add-repo \
	https://download.docker.com/linux/rhel/docker-ce.repo
$ sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

```CentOS Stream 9
$ sudo yum install -y yum-utils
$ sudo yum-config-manager \
	--add-repo \
	https://download.docker.com/linux/centos/docker-ce.repo
$ sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

```Amazon Linux
$ sudo yum install docker
```

> Docker 서비스 실행

```
$ sudo systemctl start docker
```

> 부팅했을 때 자동으로 실행하기

```
$ sudo chkconfig docker on
```

> 최신 바이너리 사용하기

배포판 버전이 오래되었거나, `CentOS`같이 버전업이 보수적인 배포판은 Docker 패키지 버전이 낮은 경우가 많다. 이번에는 배포판별 패키지가 아닌 빌드된 바이너리를 직접 사용하는 방법이다.

```
$ wget https://download.docker.com/linux/static/stable/x86_64/docker-20.10.18.tgz
$ tar vxzf docker-20.10.18.tgz
$ sudo cp docker/* /usr/local/bin
$ sudo /usr/local/bin/dockerd &
```

각 버전별 파일은 https://download.docker.com/linux/static/stable/x86_64 에서 확인할 수 있다.

# macOS

```
$ brew install --cask docker
```