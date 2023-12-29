Dockerfile은 Docker 이미지 설정 파일이다. Dockerfile에 설정된 내용대로 이미지를 생성한다.

먼저 example 디렉토리를 생성한 뒤에 example 디렉토리로 이동한다.

```
~$ mkdir example
~$ cd example
```

다음 내용은 `Dockerfile`로 저장한다.

> ./example/Dockerfile

```
FROM ubuntu:22.04
MAINTAINER pshysd <pshysd@kakao.com>

RUN apt update
RUN apt install -y nginx
RUN echo "\ndatemon off;" .> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx

VOLUME ["/data", "/etc/nginx/site-enabled", "/var/log/ngingx"]

WORKDIR /etc/nginx

CMD "[nginx]"

EXPOSE 80
EXPOSE 443
```

우분투 22.04를 기반으로 nginx 서버를 설치한 Docker 이미지를 생성하는 예제이다.

- FROM 어떤 이미지를 기반으로 할 지 설정한다.
	- Docker 이미지는 기존에 만들어진 이미지를 기반으로 생성한다.
	- <이미지 이름>:<태그> 형식
- MAINTAINER: 메인테이너 정보
- RUN: 셸 스크립트 혹은 명령을 실행한다.
	- 이미지 생성 중에는 사용자 입력을 받을 수 없으므로 `apt intall` 명령에서 `-y` 옵션을 사용한다. (yum install)의 경우도 동일
	- 나머지는 `nginx` 설정이다.
- VOLUME: 호스트와 공유할 디렉토리 목록이다. `docker run` 명령에서 `-v` 옵션으로 설정할 수 있다.
	- ex) `-v /`