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
	- `FROM ubuntu:22.04 AS builder`