Docker 명령은 기본적으로 Docker Hub를 사용한다. 이번에는 나만의 저장소 서버를 구축해보자.

Docker 저장소 서버는 Docker 레지스트리 서버라고 부른다.

`docker push` 명령으로 레지스트리 서버에 이미지를 올리고,

`docker pull` 명령으로 이미지를 받을 수 있다.

Docker 레지스트리 서버에서 이미지 데이터를 저장하는 방법은 매우 다양하다.

그 중 `Docker 레지스트리가 동작하는 서버에 저장하는 방법`과 `Amazon S3에 저장하는 방법`을 알아보자

머넞 기본에 실행되고 있는 Docker 데몬을 정지한 뒤 `--insecure-registry` 옵션을 사용하여 Docker 데몬을 실행한다.

```
$ sudo systemctl stop docker.socket
sudo systemctl stop docker
sudo dockerd --insecure-registry localhost:5000
```

보통 Docker 데몬을 직접 실행하지 않고 서비스 형태로 실행한다.

이 때는 `/etc/docker/daemon.json` 파일을 생성하고 다음과 같이 저장한다. (이 파일은 root 권한으로 수정해야 함)

```json
{
	"insecure-registries": ["localhost:5000"]
}
```

`/etc/docker/daemon.json` 파일을 수정했으면 Docker 서비스를 재시작한다.

```
$ sudo systemctl restart docker
```

# 로컬에 이미지 데이터 저장

Docker 레지스트리 서버도 Docker Hub를 통해 Docker 이미지로 제공된다.

먼저 Docker 레지스트리 이미지를 받는다

```
$ sudo docker pull registry:latest
```

`registry:latest` 이미지를 컨테이너로 실행한다.

```
$ sudo docker run -d -p 5000:5000 --name hello-registry \
	-v /tmp/registry:/tmp/registry \
	registry
```

이렇게 실행하면 이미지 파일은 호스트의 `/tmp/registry` 디렉토리에 저장된다.

![[Pasted image 20231230221129.png]]

# push 명령으로 이미지 올리기

앞에서 만든 hello:0.0.1 이미지를 개인 저장소에 올려보자

```
$ sudo docker tag hello:0.0.1 localhost:5000/hello:0.0.1
$ sudo docker push localhost:5000/hello:0.0.1
```

태그를 생성하는 명령은 

`docker tag <이미지 이름>:<태그> <Docker 레지스트리 URL>/<이미지 이름>:<태그>` 형식

이미지를 올리는 명령은

`docker push <Docker 레지스트리 URL>/<이미지 이름>:<태그>` 형식

개인 저장소에 이미지를 올릴 때는 태그를 먼저 생성해야 한다. `docker tag` 명령으로 `hello:0.0.1` 이미지를 `localhost:5000hello:0.0.1` 태그로 생성한다.

그리고 `docker push` 명령으로 `localhost:5000/hello:0.0.1` 이미지를 개인 저장소에 올린다.(태그를 생성했으므로 실제로는 hello:0.0.1 이미지가 올라간다.)

이제 다른 서버에서 개인 저장소(Docker 레지스트리 서버)에 접속하여 이미지를 받아올 수 있다. 개인 저장소 서버 IP 주소가 *172.31.23.145* 라면 다음과 같이 명령을 실행한다.

```
$ sudo docker pull 172.31.23.145:5000/hello:0.0.1
```

참고로 명령을 실행하려면 `/dtc/docker/daemon.json` 파일에서 `insecrue-registries`에 `172.31.23.145:5000`이 들어있어야 한다. 그리고 Docker 서비스를 재시작해야함.

```json
{
	"insecure-registries": ["localhost:5000", "172.31.23.145:5000"]
}
```

이미지를 삭제할 때에는 아래와 같이 실행한다.

```
$ sudo docker rmi 172.31.23.145:5000/hello:0.0.1
```

# Amazon S3에 이미지 데이터 저장

먼저 앞에서 생성한 `hello-registry` 컨테이너를 정지한다.

```
$ sudo docker stop hello-registry
```

Docker 레지스트리 이미지를 받는다

```
$ sudo docker pull registry:latest
```

`registry:latest` 이미지를 컨테이너로 실행한다.

```
$ sudo docker run -d -p 5000:5000 --name s3-registry \
	-e REGISTRY_STORAGE=s3 \
	-e REGISTRY_STORAGE_S3_BUCKET=examplebucket10 \
	-e REGISTRY_STORAGE_S3_REGION=ap-northeast-2 \
	-e REGISTRY_STORAGE_S3_ACCESSKEY=ABCDEFGHIJKLMNOPQRSTUVWXYZ \
	-e REGISTRY_STORAGE_S3_SECRETKEY=ABCDEFGHIJKLMNOPQRSTUVWXYZ \
	registry
```

S3를 사용하려면 -e 옵션으로 설정을 해줘야 한다.

- REGISTRY_STORAGE: s3를 설정한다.
- REGISTRY_STORAGE_S3_BUCKET: 이미지 데이터를 저장할 S3 버킷 이름
- REGISTRY_STORAGE_S3_REGION: s3 버킷의 리전 설정
- REGISTRY_STORAGE_S3_ACCESSKEY: AWS 액세스 키
- REGISTRY_STORAGE_S3_SECRETKEY: AWS 시크릿 키

이제 `s3-regisry` 저장소에 Docker 이미지를 push하면 S3 버킷에 저장된다.

![[Pasted image 20231230222103.png]]

