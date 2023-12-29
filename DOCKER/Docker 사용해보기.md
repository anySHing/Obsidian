Docker의 명령은 `docker run`, `docker push`와 같이 `docker <명령>` 형식이며, 항상 **root** 권한으로 실행해야 한다.

먼저 Docker의 기본적인 사용 방법을 알아보기 위해 Docker Hub에서 제공하는 이미지를 받아서 실행해보자


# search. 이미지 검색하기

Docker는 [Docker Hub](https://hub.docker.com)을 통해 이미지를 공유하는 생태계가 구축되어 있다.

유명 리눅스 배포판과 오픈 소스 프로젝트(Redis, Nginx 등)의 Docker 이미지는 모두 Docker Hub에서 구할 수 있다.

특히 이미지와 관련된 모든 명령은 기본적으로 [Docker Hub](https://hub.docker.com)를 이용하도록 설정되어 있다.

`docker search` 명령으로 Docker Hub에서 이미지를 검색할 수 있다.

```
$ sudo docker search ubuntu
```

![[스크린샷 2023-12-29 오후 4.59.03.png]]

보통 `ubuntu`, `centos`, `redis`등 OS나 프로그램 이름을 가진 이미지가 공식 이미지이다. 나머지는 사용자들이 만들어 공개한 이미지이다.

Docker Hub에서는 이미지를 검색한 뒤 해당 이미지의 `Tags` 탭을 보면 현재 사용할 수 있는 이미지의 버전을 볼 수 있다.

> **sudo 입력하지 않기**
> docker 명령은 root 권한으로 실행해야 하기 때문에 일반 계정에서는 항상 sudo를 사용한다. sudo를 매번 입력하기 귀찮기도 하고 빠뜨릴 때도 많다. sudo를 입력하지 않는 방법은 두 가지가 있다.

1. 처음부터 root 계정으로 로그인하거나 `sudo su` 명령을 사용하여 `root` 계정으로 전환

```
$ sudo su
#
```

2. 현재 계정을 docker 그룹에 포함(docker 그룹은 root 권한과 동일하므로 꼭 필요한 계정만 포함시킨다.)

```
$ sudo usermod -aG docker ${USER}
$ sudo systemctl restart docker
```
현재 계정에서 로그아웃한 뒤 다시 로그인한다.

# pull. 이미지 받기

Docker Hub에서 우분투 리눅스 이미지를 받아보자.

`docker pull <이미지 이름>:<태그>`

```
$ sudo docker pull ubuntu:latest
```


태그에 lastest를 설정할 경우 최신 버전을 받는다.

ubuntu:22.04, ubuntu:20.10처럼 태그를 지정해줄 수도 있다.

이미지 이름 앞에 예를들어 `pshysd/ubuntu` 형식으로 / 앞에 사용자 명을 붙이면 Docker Hub에서 해당 사용자가 올린 이미지를 받는다.

공식 이미지는 사용자명이 붙지 않는다.

> 참고

- 호스트에 설치된 리눅스 배포판과 Docker 이미지의 배포판의 종류가 달라도 상관없다.
	- -> CentOS에서 Ubuntu Container를 실행할 수 있다.

# images. 이미지 목록 출력하기

받은 이미지의 목록을 출력해보자.

```
$ sudo docker images
```

![[스크린샷 2023-12-29 오후 5.13.25.png]]

`docker images` 명령은 모든 이미지 목록을 출력한다. 

`docker images ubuntu` 처럼 이미지 이름을 설정하면 이름은 같지만 **태그**가 다른 이미지들이 출력된다.

# run. 컨테이너 생성하기

이미지를 컨테이너로 생성한 뒤 Bash Shell을 실행해보자.

`docker run <옵션> <이미지 이름> <실행할 파일>`

여기서는 `ubuntu` 이미지를 컨테이너로 생성한 뒤 ubuntu 이미지 안의 `/bin/bash`를 실행한다. 이미지 이름 대신 이미지 ID를 사용해도 된다.



```
$ sudo docker run -i -t --name hello ubuntu /bin/bash
```

