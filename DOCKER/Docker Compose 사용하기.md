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

![[스크린샷 2023-12-31 오후 9.43.33.png]]

`mysql>` 프롬프트가 표시되면 다음 SQL 쿼리를 입력하여 Users 테이블을 생성한다. 그리고 exit를 사용하여 프롬프트에서 빠져나온다.

```
USE db;
CREATE TABLE Users (id VARHCAR(100) NOT NULL, password VARCHAR(100) NOT NULL, PRIMARY KEY(id));
```

![[스크린샷 2023-12-31 오후 9.44.53.png]]

그리고 app 컨테이너를 생성해보자. app 디렉토리를 만들고 다음 내용을 `app.js`와 `package.json` 파일로 저장한다.

```
$ mkdir app
$ cd app
```

```js
const express = require('express');
const mysql = require('mysql');

const app = express();

app.set('PORT', 8080);

const connection = mysql.createConnection({
	host: process.env.MYSQL_HOST,
	user: process.env.MYSQL_USER,
	password: process.env.MYSQL_PASSWORD,
	database: process.env.MYSQL_DB,
});

app.get('/', (req, res) => {

connection.query(`
	INSERT INTO Users(id, password)
	VALUES("hellouser", "examplepassword");
`, (err, rows, fields) => {

	connection.query('SELECT * FROM Users;', (err, rows, fields) => {
		console.log(rows);
		res.send(rows[0]);
		});
	});
});

app.listen(app.get('PORT'), () => {
	console.log(`Express Server listening on PORT ${app.get(PORT)}`);
});
```

```json
{
	"name": "app",
	"version": "0.0.1",
	"dependencies": {
		"express": "^4.18.1",
		"mysql": "^2.18.1"
	}
}
```

이제 `node:16:alpine` 이미지로 `app.js` 파일을 실행해보자

```
$ sudo docker run -it --name app -p 8080:8080 -w /app -v ~/app:/app --network example-network \
	-e MYSQL_HOST=mysql \
	-e MYSQL_USER=root \
	-e MYSQL_PASSWORD=examplepassword \
	-e MYSQL_DB=db \
	node:16-alpine sh -c "npm install && node app.js"
```

웹 브라우저에서 `http://localhost:8080`으로 접속해보자. (Docker Desktop에서 실행했다면 http://127.0.0.1:8080)

다음과 같은 데이터가 표시되면 mySQL 데이터베이스에 정상적으로 데이터를 쓰고 읽어온 것이다.

```
{"id": "hellouser", "password":"examplepassword"}
```

위 컨테이너는 `ctrl + c`로 정지되지 않을 수 잇다. 여기서는 `ctrl + p`, `ctrl + q`를 차례대로 입력하여 컨테이너에서 빠져나오자. 

그리고 app, mysql 컨테이너를 삭제해주자

# docker-compose.yml 파일 작성하기

이제 앞에서 `docker run` 명령으로 생성해본 컨테이너들을 Docker Compose로 만들어보자.

Docker Compose는 `docker-compose.yml` 파일에 생성할 컨테이너들을 정의한다.

다음 내용을 app 디렉토리 아래에 `docker-compose.yml` 파일로 저장한다.

```yml
version: '3.8'

services:
  mysql:
    image: mysql:5.7
    volumes:
      - mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: examplepassword
      MYSQL_DATABASE: db

volumes:
  mysql:data:
```

- `docker-compose.yml`은 `version`으로 시작한다. 버전은 시간이 지남에 따라 계속 올라간다.
- services: 생성하고자 하는 컨테이너를 나열한다.
	- mysql: 컨테이너 이름이다. 여기서는 `mysql`로 정했다.
		- image: Docker 이미지 이름과 태그를 지정한다.
		- `volumes`: 데이터 볼륨 매핑이다. 여기서는 `mysql-data` 볼륨을 `/var/lib/mysql`에 연결한다. `docker volume create`로 생성한 볼륨을 `docker run` 명령에서 `-v` 옵션으로 사용하는 것과 같다.
	- `environment`: 환경 변수 설정이다. `<환경 변수 이름>: <값>` 형식으로 나열한다. `docker run` 명령의 `-e` 옵션과 같다.
- volumes: 데이터 볼륨을 생성한다. `docker volume create`와 같다.

다음 명령을 실행하여 mySQL 컨테이너를 생성해보자. (`docker compose` 명령은 반드시 `docker-compose.yml`이 있는 디렉토리에서 실행해야 한다.)

```docker
$ sudo docker compose up -d
[+] Running 3/3
⠿ Network app_default      Created 0.9s
⠿ Volume "app_mysql-data"  Created 0.2s
⠿ Container app-mysql-1    Started 3.7s
```

app_default라는 네트워크는 자동으로 생성된다(app이 앞에 붙는 이유는 현재 디렉토리 이름이 app이라서 그렇다.)

`docker ps` 명령으로 컨테이너 목록을 출력해보면 `app-mysql-1` 컨테이너가 생성된 것을 볼 수 있다.

```
$ sudo docker compose ps
NAME                COMMAND                  SERVICE             STATUS              PORTS
app-mysql-1         "docker-entrypoint.s…"   mysql               running             3306/tcp, 33060/tcp
```

`docker compose ps` 명령을 입력하면 Docker Compose로 생성한 컨테이너의 목록을 보여준다.

```
$ sudo docker compose ps
NAME               COMMAND                    SERVICE
app-mysql-1        "docker-entrypoint.s..."   mysql
```

`docker exec` 명령과 마찬가지로 Docker Compose에도 `docker compose exec` 명령이 있따. 이 명령을 사용해서 MySQL 데이터베이스에 접근해보자

```
$ sudo docker compose exec -it mysql mysql -u root -p
Enter password: 
...

mysql>
```

`mysql>` 프롬프트가 표시되면 다음 SQL 쿼리를 입력하여 `Users` 테이블을 생성한다. 그리고 `exit`를 입력하여 프롬프트에서 빠져나온다.

```
USE db;
CREATE TABLE Users (id VARHCAR(100) NOT NULL, password VARCHAR(100) NOT NULL, PRIMARY KEY(id));
```