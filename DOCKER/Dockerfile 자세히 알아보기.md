Dockerfile은 다음과 같이 `<명령> <매개 변수>` 형식으로 작성한다. `#`은 주석이다.

명령은 대소문자를 구분하지 않지만 보통 대문자로 작성한다.

```dockerfile
# 주석
FROM scratch
```

Docker는 Dockerfile에 작성된 명령을 순서대로 처리한다. 

- 명령은 항상 **FROM**으로 시작해야 한다.
	- **FROM**이 없거나 **FROM** 앞에 다른 명령이 있으면 이미지가 생성되지 않는다.
- 각 명령은 독립적으로 실행된다.
	- ex) `RUN cd /home/hello`로 디렉토리를 이동하더라도 뒤에 오는 명령에는 영향을 주지 않는다.
- 이미지를 생성할 때는 `Dockerfile`이 있는 디렉토리에서 `docker build` 명령을 사용한다

``` 
$ sudo docker build --tag example .
$ sudo docker build --tag pshysd/example .
```

`--tag` 또는 `-t` 옵션으로 이미지 이름을 설정할 수 있다.

Docker Hub에 이미지를 올리려면 `pshysd/example`처럼 / 앞에 사용자 명을 붙이면 된다.

이미지 이름을 설정하지 않아도 이미지는 생성된다. 이 때 이미지를 사용하려면 ID를 지정해야 한다.

# .dockerignore

Dockerfile과 같은 디렉터리에 들어있는 모든 파일을 컨텍스트(context)라고 한다.

특히 이미지를 생성할 때는 컨텍스트를 모두 Docker 데몬에 전송하므로 필요없는 파일이 포함되지 않도록 주의해야 한다.

컨텍스트에서 파일이나 디렉토리를 제외하고 싶을 때는 `.dockerignore` 파일을 사용한다.

Docker는 Go 언어로 작성되어 있기 때문에 파일 매칭도 Go의 규칙을 따른다.

```dockerignore
example/hello.txt
example/*.cpp
wo*
*.cpp
.git
.svn
```

특정 파일이나 디렉토리를 제외할 수 있고 보통 *를 주로 사용한다.

버전 관리 시스템을 이용하여 `Dockerfile`과 필요한 파일을 관리할 때 `.git`, `.svn`과 같은 디렉토리는 제외해준다.

# FROM

FROM은 어떤 이미지를 기반으로 이미지를 생성할 지 설정한다.

Dockerfile로 이미지를 생성할 때는 항상 기존에 있는 이미지를 기반으로 생성하기 때문에 FROM은 반드시 설정해줘야 한다.

다음과 같이 이미지 이름을 설정하거나 이미지 이름과 태그를 함께 설정할 수도 있다.

이미지 이름만 설정하면 latest를 사용한다. 또한, 이미지 이름은 생략할 수 없다.

`FROM <이미지>` 또는 `FROM <이미지>:<태그>` 형식

```dockerfile
FROM ubuntu
```

```dockerfile
FROM ubuntu:22.04
```

FROM은 항상 설정해야 하고 맨 처음에 와야 한다.

이미지를 생성할 때 FROM에 설정한 이미지가 로컬에 있으면 바로 사용하고, 없으면 Docker Hub에서 받아온다.

Dockerfile 파일 하나에 FROM을 여러 개 설정할 수도 있다. FROM을 두 개 설정했다면 이미지가 두 개 생성된다.

`--tag` 옵션으로 이미지 이름을 설정했다면 맨 마지막 FROM에 적용된다.

하지만 이런 경우보다 빌드와 파일 포함을 분리하고자 할 때 FROM을 여러 개 사용한다.

예를 들어 C 소스 파일을 컴파일 한 뒤 소스 파일은 제외하고 실행 파일만 이미지에 넣어보자.

먼저 `hello` 디렉토리를 생성한 뒤 `hello 디렉토리로 이동한다.`

```
$ mkdir hello
$ cd hello
```

다음 내용을 `hello.c`로 저장한다.

```c
$include <stdio.h>

int main()
{
	printf("Hello Docker\n");
	return 0;
}
```

그리고 다음 내용을 Dockerfile로 저장한다.

```dockerfile
FROM ubuntu:22.04 AS builder

RUN apt update
RUn apt install -y gcc
WORKDIR /tmp
ADD hello.c ./
RUN gcc hello.c -static -o hello

FROM alpine:3.16.2
WORKDIR /tmp
COPY --from=builder /tmp/hello ./

CMD ["/tmp/hello"]
```

1. `ubuntu:22.04` 이미지에 gcc를 설치한 뒤 `hello.c` 파일을 컴파일한다.
2. `apline:3.16.2` 이미지를 기반으로 `hello` 실행 파일만 추가하여 이미지를 생성한다.
	- `FROM ubuntu:22.04 AS builder`: `ubuntu:22.04` 이미지를 builder로 약칭을 지정
	- `COPY --from=builder /tmp/hello ./`: `COPY`로 파일을 복사할 때 `--from` 옵션을 지정하면 로컬이 아닌 해당 이미지에서 파일을 복사해온다. 여기서는 `--from=builder` 이므로 `FROM ubuntu:22.04 AS builder`의 builder에서 파일을 복사해온다. 즉, builder에 있는 **/tmp/hell**를 **./**로 복사한다.
3. `docker build` 명령으로 이미지를 생성한다.
	- `~/hello $ sudo docker build --tag hello:0.0.1 .`
4. 이제 `alpine:3.16.2` 이미지를 이용해서 만든 `hello:0.0.1` 이미지를 컨테이너로 생성한다.
	- `$ sudo docker run --rm hello:0.0.1`
	- `Hello Docker`
	- `Hello Docker`가 출력되면 실행 파일이 정상적으로 실행된 것
	- 이렇게 두 번의 과정을 거치는 이유는 최종 이미지 파일을 경량화하기 위해서이다.
	- 처음 `FROM ubuntu:22.04 AS builder` 에서는 apt로 gcc를 설치하기 때문에 이미지의 크기가 커진다. 그리고 실행에 필요없는 **hello.c** 소스 파일도 포함된다.
	- 여기서는 다시 `FROM alpine:3.16.2`를 사용하여 `COPY --from=builder /tmp/hello ./`로 **hello** 실행 파일만 복사하므로 크기가 매우 작아짐

이미지의 크기가 작아지면 이미지 저장 공간도 아낄 수 있고, push, pull 속도도 빨라진다.

# MAINTAINER (Deprecated)

이미지를 생성한 사람의 정보를 설정한다. 형식은 자유이며 보통 다음과 같이 이름과 이메일을 입력한다.

`MAINTAINER <작성자 정보>` 형식이다. **생략 가능하다**

```dockerfile
MAINTAINER Hong, Gildong <Hong@gildong.com>
```

# RUN

FROM에서 설정한 이미지 위에서 스크립트 혹은 명령을 실행한다. 여기서 RUN으로 실행한 결과가 새 이미지로 생성되고, 실행 내역은 이미지의 히스토리에 기록된다.

> 셸(/bin/sh)로 명령 실행하기

`RUN <명령>` 형식이며 셸 스크립트 구문을 사용할 수 있다. FROM으로 설정한 이미지에 포함된 **/bin/sh** 실행 파일을 사용하게 되며 **/bin/sh** 실행 파일이 없으면 사용할 수 없다.

```dockerfile
RUN apt install -y ngingx
RUN echo "Hello Docker" > /tmp/hello
RUN curl -sSL https://go.dev/dl/go1.19.2.src.tar.gz | tar -v -C /usr/local -xz
RUN git clone https://github.com/docker/docker.git
```

> 셸 없이 바로 실행하기

`RUN ["<실행 파일>", "<매개 변수1>", "<매개 변수2>"]` 형식이다.

실행 파일과 매개 변수를 배열 형태로 설정한다.

```dockerfile
RUN ["apt", "install", "-y", "nginx"]
RUN ["/user/local/bin/hello", "--help"]
```

FROM으로 설정한 이미지의 **/bin/sh** 실행 파일을 사용하지 않는 방식이다.

셸 스크립트 문법이 인식되지 않으므로 셸 스크립트 문법과 관련된 문자를 그대로 실행 파일에 넘겨줄 수 있다.

RUN으로 실행한 결과는 캐시되며 다음 빌드 때 재사용한다. 캐시된 결과를 사용하지 않으려면 `docker build` 명령에서 `--no-cahce` 옵션을 사용하면 된다.

# CMD

컨테이넉 시작되었을 때 스크립트 혹은 명령을 실행한다.

즉 `docker run` 명령으로 컨테이너를 생성하거나, `docker start` 명령으로 정지된 컨테이너를 시작할 때 실행된다. 

Dockerfile에서 한 번만 사용할 수 있다.

> 셸(/bin/sh)로 명령 실행하기

`CMD <명령>` 형식이며 셸 스크립트 구문을 사용할 수 있다.

FROM으로 설정한 이미지에 포함된 **/bin/sh** 실행 파일을 사용하게 되며 **/bin/sh** 실행 파일이 없으면 사용할 수 없다.

```dockerfile
CMD touch /home/hello/hello.txt
```

> 셸 없이 바로 실행하기

```dockerfile
CMD ["redis-server"]
```

> 셸 없이 바로 실행할 때 매개 변수 설정하기

`CMD ["<실행 파일>", "<매개 변수1>", "<매개 변수2>"]` 형식이다.

실행 파일과 매개 변수를 배열 형태로 설정한다.

```dockerfile
CMD ["mysqld", "--datadir=/var/lib/mysql", "--user=mysql"]
```

FROM으로 설정한 이미지의 **/bin/sh** 실행 파일을 사용하지 않는 방식이다. 셸 스크립트 문법이 인식되지 않으므로 셸 스크립트 문법과 관련된 문자를 그대로 실행 파일에 넘겨줄 수 있다.

> ENTRYPOINT를 사용하였을 때

`CMD ["<매개 변수1>", "<매개 변수2>"]` 형식이다.

ENTRYPOINT에 설정한 명령에 매개 변수를 전달하여 실행한다.

```dockerfile
ENTRYPOINT ["echo"]
CMD ["hello"]
```

Dockerfile에 ENTRYPOINT가 있으면 CMD는 ENTRYPOINT에 매개 변수만 전달하는 역할을 한다. 그래서 CMD 독자적으로 파일을 실행할 수 없게 된다.

다음과 같이 Dockerfile을 빌드하여 컨테이너를 생성하면 **hello**가 출력된다.

```
$ sudo docker build --tag example .
$ sudo docker run example
hello
```

# ENTRYPOINT

컨테이너가 시작되었을 때 스크립트 혹은 명령을 실행한다.

즉 `docker run` 명령으로 컨테이너를 생성하거나, `docker start` 명령으로 정지된 컨테이너를 시작할 때 실행된다. ENTRYPOINT는 Dockerfile에서 단 한번만 사용할 수 있다.

> 셸(/bin/sh)로 명령 실행하기

`ENTRYPOINT <명령>` 형식이며 셸 스크립트 구문을 사용할 수 있다.

FROM으로 설정한 이미지에 포함된 **/bin/sh** 실행 파일을 사용하게 되며 **/bin/sh** 실행 파일이 없으면 사용할 수 없다.

```dockerfile
ENTRYPOINT touch /home/hello/hello.txt
```

> 셸 없이 바로 실행하기

`ENTRYPOINT ["<실행 파일>", "<매개 변수1>", "<매개 변수2>"]` 형식이다.

실행 파일과 매개 변수를 배열 형태로 설정한다.

```dockerfile
ENTRYPOINT ["/home/hello/hello.sh"]
```

```dockerfile
ENTRYPOINT ["/home/hello/hello.sh", "--hello=1", "--world=2"]
```

`CMD`와 `ENTRYPOINT`는 컨테이너가 생성될 때 명령이 실행되는 것은 동일하지만 `docker run` 명령에서 동작 방식이 다르다.

> CMD의 경우

다음과 같이 Dockerfile에서 CMD로 `echo` 명령을 사용하여 **hello** 명령을 출력한다.

```dockerfile
FROM ubuntu:latest
CMD ["echo", "hello"]
```

컨테이너를 생성할 때 `docker run <이미지> <실행할 파일>` 형식이다.

이미지 다음에 실행할 파일을 설정할 수 있다. `docker run` 명령에서 실행할 파일을 설정하면 CMD는 무시된다.

```
$ sudo docker build --tag example .
$ sudo docker run example echo world
world
```

`CMD ["echo", "hello"]`는 무시되고 `docker run` 명령에서 설정한 `echo world`가 실행되어 `world`가 출력되었다. `docker run` 명령에서 설정한 `<실행할 파일>`과 Dockerfile의 CMD는 같은 기능이다.

> ENTRYPOINT의 경우

다음과 같이 Dockerfile에서 ENTRYPOINT로 `echo` 명령을 사용하여 `hello`를 출력한다.

```dockerfile
FROM ubuntu:latest
ENTRYPOINT ["echo", "hello"]
```

Dockerfile을 빌드하여 `docker run` 명령으로 실행한다.

`docker run` 명령에서 실행할 파일을 설정하면 ENTRYPOINT가 무시되지 않고, 실행할 파일 설정 자체를 매개 변수로 받아서 처리한다.

```
$ sudo docker build --tag example .
$ sudo docker run example echo world
hello echo world
```

`ENTRYPOINT ["echo", "hello"]`에서 `echo hello`가 실행되어 `hello`가 출력되고, `docker run` 명령에서 설정한 내용이 `ENTRYPOINT ["echo", "hello"]`의 매개 변수로 처리되어 `echo world`도 함께 출력된다.

셸에서는 다음과 같이 표현할 수 있다.

```
$ echo hello echo world
hello echo world
```

`echo` 명령이 아닌 다른 방식으로 실행해보자. 다음과 같이 `1 2 3 4`를 넘겨주면 그대로 `1 2 3 4`가 출력된다.

```
$ sudo docker run example 1 2 3 4
hello 1 2 3 4
```

ENTRYPOINT는 `docker run` 명령에서 `--entrypoint` 옵션으로도 설정할 수 있다. `--entrypoint` 옵션으로 `cat`을 실행하고 **/etc/hostname** 파일의 내용을 출력해보자

```
$sudo docker run --entrypoint"cat" example /etc/hostname
12yf1gy41f2g1veq
```

`--entrypoint` 옵션을 설정하면 Dockerfile에 설정한 ENTRYPOINT는 무시된다.

# EXPOSE

호스트와 연결할 포트 번호를 설정한다. `docker run` 명령의 `--expose` 옵션과 동일하다.

`EXPOSE <포트 번호>` 형식이다.

```dockerfile
EXPOSE 80
EXPOSE 443

____________

EXPOSE 80 443
```

EXPOSE 하나로 포트 번호를 두 개 이상 동시에 설정할 수도 있다.

EXPOSE는 호스트와 연결만 할 뿐 외부에 노출은 되지 않는다.

포트를 외부에 노출하려면 `docker run` 명령의 `-p`, `-P` 옵션을 사용해야 한다.

# ENV

환경 변수를 설정한다. ENV로 설정한 환경 변수는 RUN, CMD, ENTRYPOINT에 적용된다.

`ENV <환경 변수> <값>` 형식이다. 환경 변수를 사용할 때는 `$`를 사용한다.

```dockerfile
ENV GOPATH /go
ENV PATH /go/bin:$PATH
```

다음은 ENV에서 설정한 환경 변수를 CMD로 출력한다.

```dockerfile
ENV HELLO 1234
CMD echo $HELLO
```

Dockerfile을 빌드하여 `docker run` 명령으로 실행한다.

```
$ sudo docker build --tag example .
$ sudo docker run example
1234
```

ENV에서 설정한 **HELLO**의 값 **1234**가 출력된다.

환경 변수는 `docker run` 명령에서도 설정할 수 있다.

`-e <환경 변수> <값>` 형식이다.

```
$ sudo docker run -e Hello=4321 example
4321
```

`-e` 옵션은 여러 번 사용할 수 있고, `--env` 옵션과 동일하다.

# ADD

파일을 이미지에 추가한다.

`ADD <복사할 파일 경로> <이미지에서 파일이 위치할 경로>` 형식이다.

```dockerfile
ADD hello-entrypoint.sh /entrypoint.sh
ADD hello-der /hello-dir
ADD zlib-1.2.8.tar.gz /
ADD hello.zip /
ADD http://example.com/hello.txt /hello.txt
ADD *.txt /root/
```

- `<복사할 파일 경로>`는 컨텍스트 아래를 기준으로 하며 컨텍스트 바깥의 파일, 디렉터리나 절대 경로는 사용할 수 없다.
	- ex) `ADD ../hello.txt /home/hello` (x)
	- ex) `ADD /home/hello/hello.txt /home/hello` (x)
- `<복사할 파일 경로>`는 파일 뿐만 아니라 디렉토리도 설정할 수 있음, 디렉토리를 지정하면 디렉토리의 모든 파일을 복사한다. 또한, 와일드 카드를 사용하여 특정 파일만 복사할 수 있다.
	- ex) `ADD *.txt /root/`
- `<복사할 파일 경로>`에 인터넷에 있는 파일의 URL을 설정할 수 있다.
	- `<이미지에서 파일이 위치할 경로>`의 마지막에 /가 있으면 디렉토리가 생성되고 파일은 그 아래에 복사된다.
		- ex) `ADD http://example.com/hello.txt /home/hello/`와 같이 설정하면 `/home/hello/hello.txt`에 파일이 복사된다.
- 로컬에 있는 압축 파일(tar.gz, tar.bz2, tar.xz)은 압축을 해제하고 tar를 풀어서 추가된다. 단, 인터넷에 있는 파일 URL은 압축만 해제한 뒤 tar 파일이 그대로 추가된다.
	- ex) `ADD hello.tar.gz /` (압축을 해제하고 tar를 풀어서 추가한다.)
	- ex) `ADD http://zlib.net/zlib-1.2.8.tar.gz /`(gzip 압축만 해제한 뒤 tar 파일을 추가한다. 단 파일 내용은 tar 이지만 파일 이름은 zlib-1.2.8.tar.gz 처럼 .gz가 붙어있다.)
- `<이미지에서 파일이 위치할 경로>`는 항상 절대 경로로 설정해야 한다. 그리고 마지막이 `/`로 끝나면 디렉토리가 생성되고 파일은 그 아래에 복사된다.
- `ADD ./ /hello`와 같이 현재 디렉토리를 추가할 때 `.dockerignore` 파일에 설정한 파일과 디렉토리는 제외된다.

> 인터넷의 파일 URL을 압축 해제하여 추가하기
> 앞에서 설명한 것처럼 ADD는 파일 URL을 압축만 해제하고 tar는 해제하지 않는다.
> 이 때는 RUN으로 `curl`이나 `wget`으로 파일을 받은 뒤 압축을 해제하면 된다.

> 우분투

```dockerfile
FROM ubuntu:latest
RUN apt update
RUN apt install -y curl
RUN curl http://zlib.net/zlib-1.2.8.tar.gz | tar -xz
```

```dockerfile
FROM ubuntu:latest
RUN apt update
RUN apt install -y wget
RUN wget http://zlib.net/zlib-1.2.8.tar.gz -9 - | tar -xz
```

> CentOS

```dockerfile
FROM centos:latest
RUN yum install -y curl tar
RUN curl http://zlib.net/zlib-1.2.8.tar.gz | tar -xz
```

```dockerfile
FROM centos:latest
RUN yum install -y wget tar
RUN wget http://zlib.net/zlib-1.2.8.tar.gz -9 - | tar -xz
```

다음은 tar, tar.gz, tar.gz2, tar.xz 파일의 압축을 해제하는 방법이다.
- `ubuntu:latest`는 bzip2가 설치되어 있고, xz는 설치되어 있지 않다. xz는 apt-get으로 xz-utils 패키지를 설치한다.
- centos:latest는 xz가 설치되어 있고, bz2는 설치되어 있지 않다. bz2는 yum으로 bzip2 패키지를 설치한다.

```dockerfile
RUN curl http://example.com/hello.tar | tar -x
RUN curl http://example.com/hello.tar.gz | tar -xz
RUN curl http://example.com/hello.tar.bz2 | tar -xj
RUN curl http://example.com/hello.tar.xz | tar -xJ
```

`ADD`로 추가되는 파일은 소유자(UID) 0, 그룹(GID) 0으로 설정되고 권한은 기존 파일의 권한을 따른다. URL로 추가하면 권한은 600으로 설정된다.

> 참고
> UID 0, GID 0은 root 계정이다.

# COPY

파일을 이미지에 추가한다. ADD와는 달리 COPY는 압축 파일을 추가할 때 압축을 해제하지 않고, 파일 URL도 사용할 수 없다.

`COPY <복사할 파일 경로> <이미지에서 파일이 위치할 경로>` 형식이다.

```dockerfile
COPY hello-entrypoint.sh entrypoint.sh
COPY hello-dir /hello-dir
COPY zlib-1.2.8.tar.gz / zlib-1.2.8.tar.gz
COPY *.txt /root/
```

- `<복사할 파일 경로>`는 컨텍스트 아래를 기준으로 하며 컨텍스트 바깥의 파일, 디렉토리나, 절대 경로는 사용할 수 없다.
	- ex) `COPY ../hello.txt /home/hello` (x)
	- ex) `COPY /home/hello/hello.txt /home/hello` (x)
- `<복사할 파일 경로>`는 파일 뿐만 아니라 디렉토리도 설정할 수 있으며, 디렉토리를 지정하면 디렉토리의 모든 파일을 복사한다. 또한, 와일드 카드를 사용하여 특정 파일만 복사할 수 있다.
	- ex) `COPY *.txt /root/`
- `<복사할 파일 경로>`에 인터넷에 있는 파일의 URL은 사용할 수 없다.
- 압축 파일은 압축을 해제하지 않고 그대로 복사된다.
- `<이미지에서 파일이 위치할 경로>`는 항상 절대 경로로 설정해야 한다. 그리고 마지막이 `/`로 끝나면 디렉토리가 생성되고 파일은 그 아래에 복사된다.
- `COPY ./ /hello`와 같이 현재 디렉토리를 추가할 때 **.dockerignore** 파일에 설정한 파일과 디렉토리는 제외된다.

COPY로 추가되는 파일은 소유자(UID) 0, 그룹(GID) 0으로 설정되고, 권한은 기존 파일의 권한을 따른다.

# VOLUME

디렉토리의 내용을 컨테이너에 저장하지 않고 호스트에 저장하도록 설정한다.

`VOLUME <컨테이너 디렉토리>` 또는 `VOLUME ["컨테이너 디렉토리 1", "컨테이너 디렉토리2"]` 형식이다.

```dockerfile
VOLUME /data
VOLUME ["/data", "/var/log/hello"]
```

`/data`처럼 바로 경로를 설정할 수도 있고, `["/data", "/var/log/hello"]`처럼 배열 형태로 설정할 수도 있다.

단, VOLUME으로는 호스트의 특정 디렉토리와 연결할 수는 없다.

데이터 볼륨을 호스트의 특정 디렉토리와 연결하려면 `docker run` 명령에서 `-v` 옵션을 사용해야 한다.

옵션은 `-v <호스트 디렉토리>:<컨테이너 디렉토리>` 형식이다.

```
$ sudo docker run -v /root/data:/data example
```

# USER

명령을 실행할 사용자 계정을 설정한다.

RUN, CMD, ENTRYPOINT에 적용된다.

`USER <계정 사용자명>` 형식이다.

```dockerfile
USER nobody
```

USER 뒤에 오는 모든 RUN, CMD, ENTRYPOINT에 적용되며, 중간에 다른 사용자를 설정하여 사용자를 바꿀 수 있다.

```dockerfile
USER nobody
RUN touch /tmp/hello.txt

USER root
RUN touch /hello.txt
ENTRYPOINT /hello-entrypoint.sh
```

처음에는 `nobody` 계정으로 `/tmp/hello.txt` 파일을 생성한다. 그 다음부터는 `root` 계정으로 `/hello.txt` 파일을 생성하고(/에는 root 계정만 파일을 생성할 수 있음), `/hello-entrypoint.sh` 파일을 실행한다.

# WORKDIR

RUN, CMD, ENTRYPOINT의 명령이 실행될 디렉토리를 설정한다.

`WORKDIR <경로>` 형식이다.

```dockerfile
WORKDIR /var/www
```

WORKDIR 뒤에 오는 모든 RUN, CMD, ENTRYPOINT에 적용되며, 중간에 다른 디렉토리를 설정하여 실행 디렉토리를 바꿀 수 있다.

```dockerfile
WORDIR /root
RUN touch hello.txt

WORKDIR /tmp
RUN touch hello.txt
```

WORKDIR은 절대 경로 대신 상대 경로도 사용할 수 있다. 상대 경로를 사용하면 먼저 설정한 WORKDIR의 경로를 기준으로 디렉토리를 변경한다. 최초 기준은 `/`이다.

```dockerfile
WORKDIR var
WORKDIR www

RUN touch hello.txt
```

상대 경로를 사용하여 `/` -> `/var` -> `/var/www`로 이동했기 때문에

`/var/www/hello.txt` 파일이 생성되었다.

# ONBUILD

생성한 이미지를 기반으로 다른 이미지가 생성될 때 명령을 실행(trigger)한다.

최초에 ONBUILD를 사용한 상태에서는 아무 명령도 실행하지 않는다.

다음번에 이미지가 FROM으로 사용될 때 실행할 명령을 예약하는 기능이라 할 수 있다.

`ONBUILD <Dockerfile 명령> <Dockerfile 명령의 매개 변수>` 형식이다.

MAINTAINER, ONBUILD를 제외한 모든 Dockerfile 명령을 사용할 수 있다.

```dockerfile
ONBUILD RUN touch /hello.txt
ONBUILD ADD world.txt /world.txt
```

ONBUILD는 이미지를 생성한 뒤 해당 이미지를 기반으로 커스터마이징을 할 때 활용할 수 있다.

다음과 같이 ONBUILD를 사용하여 `RUN touch /hello.txt`를 실행하도록 설정한다.

```dockerfile
FROM ubuntu:latest
ONBUILD RUN touch /hello.txt
```

`docker build` 명령으로 `example` 이미지를 생성한 뒤 `docker run` 명령으로 컨테이너를 생성한다. 컨테이너의 Bash 셸이 실행되면 `ls` 명령으로 `/`의 파일 목록을 출력한다.

```
$ sudo docker build --tag example .
$ sudo docker run -i -t example /bin/bah
root@weqweqwrs:/# ls
bin boot dev etc home lib lib32 libt64 libx32 media ...
```

ONBUILD로 설정했기 때문에 `example` 이미지에는 `/hello.txt` 파일이 생성되지 않았다.

이제 FROM을 사용하여 `example` 이미지를 기반으로 새 이미지를 생성한다.

```dockerfile
FROM example
```

`docker build` 명령으로 example2 이미지를 생성한 뒤 `docker run` 명령으로 컨테이너를 생성한다. 컨테이너의 Bash 셸이 실행되면 `ls` 명령으로 `/`의 파일 목록을 출력한다.

```
$ sudo docker build --tag example2 .
Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM example
# Executing 1 build trigger
---> Running in 51affb087bfb
Removing intermediate container 51affb087bfb
---> da3e74dad967
Successfully built da3e74dad967
Successfully tagged example2:latest
$ sudo docker run -i -t example2 /bin/bash
root@82656a94d554:/# ls
bin  boot  dev  etc  hello.txt  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

`docker build` 명령을 실행할 때 `# Executing 1 build triggers`라고 출력되고 그 아래부터 ONBUILD로 설정한 명령이 실행된다.

이제 ONBUILD를 통해 `exmaple2` 이미지에 `/hello.txt` 파일이 생성되었다.

ONBUILD는 바로 아래 자식 이미지를 생성할 때만 적용되고, 손자 이미지에는 적용되지 않는다(상속되지 않는다.)

> `docker inspect` 명령으로 이미지의 ONBUILD 설정을 확인할 수 있다.

```
$ sudo docker inspect -f "{{ .ContainerConfig.Onbuild}}"
[RUN touch /hello.txt]
```