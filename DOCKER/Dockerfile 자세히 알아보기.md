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

