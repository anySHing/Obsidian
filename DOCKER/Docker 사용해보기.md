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

```
$ sudo docker run -i -t --name my-first-container ubuntu /bin/bash
```

여기서는 `ubuntu` 이미지를 컨테이너로 생성한 뒤 ubuntu 이미지 안의 `/bin/bash`를 실행한다. 이미지 이름 대신 이미지 ID를 사용해도 된다.

- `-i`(interactive), `-t`(Pseudo-tty) 옵션을 사용하면 실행된 Bash Shell에 입력 및 출력을 할 수 있다.
- `--name` 옵션으로 컨테이너의 이름을 지정할 수 있다. 이름을 지정하지 않으면 Docker가 자동으로 생성한다.

이제 호스트 OS와 완전히 격리된 공간이 생성되었다. `cd`, `ls` 명령으로 컨테이너 내부를 한번 둘러보자.

![[스크린샷 2023-12-29 오후 5.47.19.png]]

호스트 OS(현재 macOS)와는 다른 도커 컨테이너의 OS가 실행 중인것을 확인할 수 있다.

`exit`를 입력하여 Bash Shell에서 빠져나오면 컨테이너가 정지(stop)된다.

# ps. 컨테이너 목록 확인하기

`docker ps`

`-a, --all` 옵션을 사용하면 정지된 컨테이너까지 모두 출력하고, 옵션을 사용하지 않을 경우 실행되고 있는 컨테이너만 출력한다.

![[스크린샷 2023-12-29 오후 5.50.36.png]]

# start. 컨테이너 시작하기

방금 정지한 컨테이너를 다시 시작해보자.

`docker start <컨테이너 이름 | 컨테이너 ID>`

```
$ sudo docker start my-first-container
```

# restart. 컨테이너 재시작하기

OS 재부팅처럼 컨테이너를 재시작한다.

`docker restart <컨테이너 이름 | 컨테이너 ID>

```
$ sudo docker restart my-first-container
```

# attach. 컨테이너 접속하기

시작한 컨테이너에 접속해보자.

`docker attach <컨테이너 이름 | 컨테이너 ID>`

```
$ sudo docker attach my-first-container
```

![[스크린샷 2023-12-29 오후 5.53.53.png]]

우리는 `/bin/bash`를 실행했기 때문에 명령을 자유롭게 입력할 수 있지만, DB나 서버 애플리케이션을 실행하면 입력은 할 수 없고 출력만 보게 된다.

Bash Shell에서 `exit` 또는 `Ctrl + D`를 입력하면 컨테이너가 정지된다.

`Ctrl + P`, `Ctrl + Q`를 차례대로 입력하면 컨테이너를 정지하지 않고 컨테이너를 빠져나올 수 있다.

# exec. 외부에서 컨테이너 안의 명령 실행하기

이번에는 `/bin/bash`를 통하지 않고 외부에서 컨테이너 안의 명령을 실행해보자

`docker exec <컨테이너 이름 | 컨테이너 아이디> <명령> <매개 변수>`

컨테이너가 실행되고 있는 상태에서만 사용할 수 있다.

```
$ sudo docker exec my-first-container echo "hello world"
hello world
```

![[스크린샷 2023-12-29 오후 5.57.29.png]]

컨테이너 안의 `echo` 명령을 실행하고 매개 변수로 `hello world`를 입력했으므로 `hello world`라고 출력된다.

`docker exec` 명령은 이미 실행된 컨테이너에 `apt-get`, `yum` 명령으로 패키지를 설치하거나, 각종 데몬을 실행할 때 활용할 수 있다.

# stop. 컨테이너 정지하기

`docker stop <컨테이너 이름 | 컨테이너 ID`

```
$ sudo docker stop my-first-container
```

![[스크린샷 2023-12-29 오후 5.59.57.png]]

1. `my-first-container` 컨테이너 실행
2. 현재 실행중인 컨테이너의 목록을 출력. `my-first-container` 확인
3. `my-first-container` 컨테이너 정지
4. 현재 실행중인 컨테이너의 목록을 출력. `my-first-container`가 존재하지 않음

# rm. 컨테이너 삭제하기

`docker rm <컨테이너 이름 | 컨테이너 ID>`

```
$ sudo docker rm my-first-container
```

![[스크린샷 2023-12-29 오후 6.02.42.png]]

1. `my-first-container` 컨테이너 삭제 명령 실행
2. 모든 컨테이너 목록 출력 -> `my-first-container`가 삭제되었으므로 아무것도 존재하지 않음

# rmi. 이미지 삭제하기

`docker rmi <이미지 이름 | 이미지 ID>:<태그>`

```
$ sudo docker rmi ubuntu:latest
```

> **주의!**
> `docker rmi ubuntu`처럼 이미지 이름만 지정하면 **ubuntu** 이름을 가진 모든 이미지가 삭제된다.

![[스크린샷 2023-12-29 오후 6.05.08.png]]

1. `ubuntu:latest` 이미지 삭제 명령 실행
2. 현재 내 도커의 이미지들 출력 -> 아무것도 존재하지 않음