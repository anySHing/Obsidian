# Bash 익히기

Docker가 리눅스 기반이기 때문에 이미지를 생성할 때 Bash(Bourne-again shell)를 주로 사용한다. 그래서 이미지를 생성하기 전에 자주 사용하는 Bash 문법을 간단히 알아본다.

| 문법 | 설명 |
| :--: | :--: |
| > | 출력 리다이렉션.<br>명령 실행의 표준 출력(stdout)을 파일로 저장한다.<br>유닉스 계열 운영 체제는 장치도 파일로 처리하기 때문에 명령 실행 결과를 특정 장치로 보낼 수도 있다.<br>```$ echo "hello" > ./hello.txt```<br>```$ echo "hello" > /dev/null``` |
| < | 입력 리다이렉션.<br>파일의 내용을 읽어 명령의 표준 입력(stdin)으로 사용한다.<br>```$ cat < ./hello.txt``` |
| >> | 명령 실행의 표준 출력(stdout)을 파일에 추가한다.<br>>는 이미 있는 파일에 내용을 덮어쓰지만 >>는 파일 뒷부분에 내용을 추가한다.<br>```$ echo "world" >> ./hello.txt``` |
| 2> | 명령 실행의 표준 에러(stderr)를 파일로 저장한다. |
| 2>> | 명령 실행의 표준 에러(stderr)를 파일에 추가한다. |
| &> | 표준 출력과 표준 에러를 모두 파일로 저장한다. |
| 1>&2 | 표준 출력을 표준 에러로 보낸다. `echo` 명령으로 문자열을 표준 출력으로 출력했지만 표준 에러로 보냈기 때문에 변수에는 문자열이 들어가지 않는다.<br>```$ hello=$(echo "Hello World" 1>&2)```<br>```$ echo $hello``` |
| 2>&1 | 표준 에러를 표준 출력으로 보낸다. abcd 라는 명령은 없으므로 에러가 발생하지만 에러를 표준 출력으로 보낸 뒤 다시 /dev/null로 보냈기 때문에 아무것도 출력되지 않는다.<br>```$ abcd > /dev/null 2>&1``` |
| \| | 파이프.<br>명령 실행의 표준 출력을 다른 명령의 표준 입력으로 보낸다.<br>즉 첫번째 명령의 출력 값을 두 번째 명령에서 처리한다.<br>```$ ls -al \| grep .txt``` |
| $ | Bash의 변수이다.<br>값을 저장할 때는 \$를 붙이지 않고, 변수를 가져다 쓸 때만 \$를 붙인다.<br>```$ hello="Hello World"```<br>```$ echo $hello```<br>```Hello World``` |
| $() | 명령 실행 결과를 변수화한다.<br>명령 실행 결과를 변수에 저장핳거나 다른 명령의 매개 변수로 넘겨줄 때 사용한다.<br>또는 문자열 안에 명령의 실행 결과를 넣을 때 사용한다.<br>```$ sudo docker rm $(docker ps -aq)```<br>```echo $(date)```<br>```Tue Sep 9 21:24:30 KST 2014``` |
| `` | \$()와 마찬가지로 명령 실행 결과를 변수화한다.<br>```$ sudo docker rm `docker ps -aq```<br>```echo `date` ```<br>```Tue Sep 9 21:24:30 KST 2014``` |
| && | 한 줄에서 명령을 여러 개 실행한다.<br>단 앞에 있는 명령이 에러 없이 실행되어야 뒤에 오는 명령이 실행된다.<br>```$ make && make install``` |
| ; | 한 줄에서 명령을 여러 개 실행한다.<br>앞에 있는 명령이 실패해도 뒤에 오는 명령이 실행된다.<br>```$ false; echo "Hello"```<br>```Hello``` |
| '' | 문자열.<br>'' 안에 들어있는 변수는 처리되지 않고 변수명 그대로 사용된다.<br>또한 ``와 \$()도 처리되지 않고 그대로 사용된다.<br>```$ echo '$USER'```<br>```$USER``` |
| "" | 문자열.<br>명령에 문자열 매개 변수를 입력하거나 변수에 저장할 때 주로 사용한다.<br>''와 달리 "" 안에 변수가 들어있으면 변수의 내용으로 바뀐다.<br>또한 \`\`와 \$()도 실행 결과 값이 사용된다.<br>```$ echo "Hello World"```<br>```Hello World```<br>```$ echo "$USER"```<br>```pshysd```<br>```$ echo "Host name is $(hostname)"```<br>```Host name is ubuntu```<br>```echo "Time: `date` ```<br>```Time: Tue Sep 9 21:28:10 KST 2014``` |
| "''" | " "안에 ''가 들어갈 수 있다.<br>명령 안에서 다시 명령을 실행하고매개 변수를 지정할 때 사용한다.<br>```$ bash -c "/bin/echo Hello 'World'"```<br>```Hello World``` |
| "\\$hello | ' ' 안에서 \"를 사용할 때는 \"처럼 앞에 \를 붙여준다.<br>```$ bash -c "/bin/echo '{ \"user\": \"$USER\" }'"```<br>```{ "user": "pshysd"}``` |
| ${} | 변수 치환(substitution)이다.<br>\"\" 문자열 안에서 변수를 출력할 때 주로 사용한다.<br>\${}대신 \$만 사용해도 된다.<br>```$ str="World"```<br>```$ echo "Hello ${str}```<br>```Hello World```<br>스크립트에서 변수의 기본 값을 설정할 때도 사용한다. 다음은 HELLO 변수가 있으면 그대로 사용하고 변수가 없으면 기본 값으로 설정한 abcd를 대입한다.<br>```$ HELLO=```<br>```$ HELLO=${HELLO-"abcd"}```<br>```$ echo $HELLO```<br>값이 NULL인 HELLO 변수가 이미 있기 때문에 기본 값을 대입하지 않는다.<br>다음은 변수에 값이 있으면 그대로 사용하고, 값이 NULL이면 기본 값으로 설정한 abcd를 대입한다.<br>```$ WORLD=```<br>```$ WORLD=${WORLD:-"abcd"}```<br>```$ echo $WORLD```<br>```abcd``` |
| \ | 한 줄로 된 명령을 여러 줄로 표현할 때 사용한다.<br>```$ sudo docker run -d --name hello busybox:latest```<br>```$ sudo docker run \```<br>``` -d \```<br>```--name hello \```<br>```busybox: latest``` |
| {1..10} | 연속된 숫자를 표현한다.<br>{시작 숫자..끝 숫자} 형식<br>```$ echo {1..10}```<br>```1 2 3 4 5 6 7 8 9 10``` |
| {문자열 1, 문자열2} | \{\} 안에 문자열을 여러 개 지정하여 명령 실행 횟수를 줄인다.<br>다음은 hello.txt, world.txt 두 파일을 한번에 hello-dir 디렉토리 아래에 복사한다.<br>```$ cp ./{hello.txt, world.txt} hello-dir/``` |
| if | if 조건문.<br><br>```if [ $a -eq $b ]; then```<br>```echo $a```<br>```fi```<br><br>숫자 비교<br>-eq: 같다<br>-ne: 같지 않다<br>-gt: 초과<br>-ge: 이상<br>-lt: 미만<br>-le: 이하<br><br>문자 비교<br>=, \==: 같다<br>!=: 같지 않다<br>-z: 문자열이 NULL일 경우<br>-n : 문자열이 NULL이 아닐 경우 |
| for | for 반복문.<br><br>```for i in $(ls)```<br>```do```<br>  ```echo $i```<br>```done```<br><br>```for (( i=0; i < 10; i++ ))```<br>```do```<br>  ```echo $i```<br>```done```<br><br>```NUM=(1 2 3)```<br>```for i in ${NUM[@]}```<br>```do```<br>  ```echo $i```<br>```done``` |
| while | while 반복문.<br><br>```while :```<br>```do```<br>```echo "Hello World;"```<br>```sleep 1;```<br>```done``` |
| <<< | 문자열을 명령(프로세스)의 표준 입력으로 보낸다.<br>```$ cat <<< "User name is $USER"```<br>```User name is pshysd``` |
| <<EOF<br>EOF | 여러 줄의 문자열을 명령(프로세스)의 표준 입력으로 보낸다.<br><br>```$ cat > ./hello.txt <<EOF```<br>```Hello World```<br>```Host name is $(hostname)```<br>```User name is $(USER)```<br>```EOF```<br><br>cat은 파일이나 표준 입력의 내용을 출력한느 명령이다.<br>cat의 표준 출력을 ./hello.txt로 저장하고, <<EOF로 문자열을 cat의 표준 입력으로 보낸다.<br>이렇게 하면 문자열 3줄이 ./hello.txt 파일에 저장된다. |
| export | 설정한 값을 환경 변수로 만든다.<br>`export <변수>=<값` 형식<br><br>```$ export HELLO=world``` |
| printf | 지정한 형식대로 값을 출력한다.<br>파이프와 연동하여 명령(프로세스)에 값을 입력하는 효과를 낼 수 있다.<br><br>`$ printf 80\\nexampleuser\\ny \| example-config`<br>`Port: 80`<br>`User: exampleuser`<br>`Save Configuration (y/n): y`<br><br>예를 들어 `example-config`는 Port, User, Save Configuration을 사용자에게 입력받는다.<br>printf로 미리 값을 설정하여 파이프로 example-config에게 넘겨주면 사용자가 입력하지 않아도 자동으로 값이 입력된다. |
| sed | 텍스트 파일에서 문자열을 변경한다.<br>hello.txt 파일의 내용 중에서 hello 라는 문자열을 찾아서 world 문자열로 바꾸려면 다음과 같이 실행한다.<br><br>`$ sed -i "s/hello/world/g" hello.txt`<br><br>`sed -i "s/<찾을 문자열>/<바꿀 문자열>/g <파일명>` 형식이다. |
| # | 주석 |


# Dockerfile 작성하기

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
	- ex) `-v /root/data:/data`는 호스트의 `root/data` 디렉토리를 Docker 컨테이너의 `/data` 디렉토리 연결한다.
- CMD: 컨테이너가 시작되었을 때 실행할 실행 파일 또는 셸 스크립트이다.
- WORKDIR: CMD에서 설정한 실행 파일이 실행될 디렉토리
- EXPOSE: 호스트와 연결할 포트 번호

# build. 이미지 생성하기

Dockerfile을 작성했으면 이미지를 생성한다.

Dockerfile이 저장된 example 디렉토리에서 다음 명령을 실행한다.

`docker build <옵션> <Dockerfile 경로>` 형식이다.

```
~/example $ sudo docker build --tag hello:0.1 .
```

`--tag` 옵션으로 이미지 이름과 태그를 설정할 수 있다.

이미지 이름만 설정할 경우 태그는 **latest**로 자동 설정된다.

잠시 기다리면 이미지 파일이 생성된다. 이미지 목록을 출력해보자

![[스크린샷 2023-12-29 오후 7.33.35.png]]

`hello:0.0.1` 이미지가 생성되었다. 이제 실행해보자.

```
$ sudo docker run --name hello-nginx -d -p 80:80 -v /Users:/data hello:0.0.1
```

그 뒤 `127.0.0.1` 또는 `localhost:80`으로 접속하면 Welcome to NginX! 텍스트가 보인다.