# history. 이미지 히스토리 살펴보기

앞에서 생성한 `hello:0.0.1` 이미지의 히스토리를 조회해보자

`docker history <이미지 이름 | 이미지 ID>:<태그>`

```
$ sudo docker history hello:0.0.1
```

![[스크린샷 2023-12-30 오전 3.31.28.png]]

# cp. 파일 꺼내기

`hello-nginx` 컨테이너에서 파일을 꺼내보자

`docker cp <컨테이너 이름>:<경로> <호스트 경로>`

```
$ sudo docker cp hello-nginx:/etc/nginx/nginx.conf ./
```

![[스크린샷 2023-12-30 오전 3.34.17.png]]

현재 디렉토리에 `nginx.conf` 파일이 복사되었다.

# commit. 컨테이너의 변경사항을 이미지로 생성하기

`docker commit` 명령은 컨테이너의 변경 사항을 이미지 파일로 생성한다.

`hello-nginx` 컨테이너 안의 팡리 내용이 바뀌었다고 치고, 컨테이너를 이미지 파일로 생성해보자

`docker commit <옵션> <컨테이너 이름 | 컨테이너 ID> <이미지 이름>:<태그>`

```
$ sudo docker commit -a "Foo Bar <foo@bar.com>" -m "add hello.txt" hello-nginx hello:0.0.2
```

![[스크린샷 2023-12-30 오전 3.37.16.png]]

`-a "Foo Bar <foo@bar.com>"`과 `-m "add hello.txt` 옵션으로 커밋한 사용자와 로그 메시지를 설정한다. `hello-nginx` 컨테이너를 `hello:0.0.2` 이미지로 생성한다.

이미지 목록을 출력해보자

![[스크린샷 2023-12-30 오전 3.38.44.png]]

`hello:0.0.2` 이미지가 생성되었다.

# diff. 컨테이너에서 변경된 파일 확인하기

`docker diff` 명령은 컨테이너가 실행되면서 변경된 파일 목록을 출력한다.

비교 기준은 컨테이너를 생성한 이미지 내용이다.

`docker diff <컨테이너 이름 | 컨테이너 ID>` 형식

![[스크린샷 2023-12-30 오후 10.00.45.png]]

- `A`: 추가된 파일
- `C`: 변경된 파일
- `D`: 삭제된 파일

# inspect. 세부 정보 확인하기

`docker inspect` 명령은 이미지와 컨테이너의 세부 정보를 출력한다.

