지금까지 Dockerfile로 이미지를 생성해보았다.

Docker에서는 여러 컨테이너를 손쉽게 생성할 수 있도록 **Docker Compose**라는 기능을 제공한다.

예를 들어 다음과 같이 app 컨테이너와 mySQL 컨테이너 두 개를 생성하고, app 컨테이너에서 mySQL 데이터베이스에 접속할 수 있도록 구성할 수 있다.

![[Pasted image 20231231213509.png]]

# docker run으로 컨테이너 생성하기

Docker Compose를 사용하기 전에 먼저 `docker run` 명령으로 mySQL 컨테이너를 생성해보자.

여기서는 `--link` 옵션 대신 `docker network create` 명령으로 네트워크를 생성하여 app 컨테이너가 mySQL 컨테이너에 접근할 수 있도록 한다.

```
$ sudo docker network create example-network
$ sudo docker run -d --name mysql --network example-network -v mysql-data:/var/lib/mysql \
	-e MYSQL_ROOT_PASSWORD=examplepassword \
	-e MYSQL_DATABASE=db
	mysql:5.7
```

이제 `mysql` 명령으로 데이터베이스에 접근해보자

다음과 같이 `docker exec` 명령을 사용하여 mysql 컨테이너 안의 `mysql` 명령을 실행한다. `Enter Password: `가 표시되면 `examplepassword`를 입력한다.

