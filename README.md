도커 로그인
```
docker login
```

도커 이미지 검색
- d로 시작하는 이미지 모두 출력

```
$ docker images d*
REPOSITORY            TAG       IMAGE ID       CREATED       SIZE 
deepasmr_django       latest    336db144cf0d   8 days ago    994MB
drf_tutorial_django   latest    cb3899be2008   10 days ago   974MB

```

도커 이미지 태그명 변경

```
docker image tag [before tag] [after tag]
```


도커 레지스트리에서 nginx 이미지 검색
```
docker search nginx
```

도커 레지스트리에서 nginx 최신 버전 이미지 다운

```
docker pull nginx:latest
```

web이라는 컨테이너로 ginx 이미지를 받아 80 포트로 도커 데몬을 백그라운드(-d)로 실행
```
docker run -d --name web -p 80:80 nginx:latest
```

도커 중지
```
docker stop web
```

도커 허브에 컨테이너 업로드 

    - docker image tag [이미지] [아이디/이미지]
    - push 하려는 이미지의 tag를 설정하고 그 이미지를 push 한다.
    - docker hub의 아이디와 일치해야한다.
    - 이때 아이디가 틀릴 경우 `denied: requested access to the resource is denied`  에러가 발생한다. 
```
$ docker image tag  drf_tutorial_django:latest ithingvvv/drf-tutorial_django:latest

$ docker images
REPOSITORY                    TAG       IMAGE ID       CREATED       SIZE 
drf_tutorial_django           latest    cb3899be2008   10 days ago   974MB```

$ docker push ithingvvv/drf-tutorial_django
Using default tag: latest
The push refers to repository [docker.io/ithingvvv/drf-tutorial_django]
1683df5862f4: Preparing

private registry를 운영할 경우
- 도커 허브에서 [docker registry](https://hub.docker.com/_/registry) 이미지를 사용해야한다.
- image repository
  - ex)
  - localhost:5000/ubuntu:18.04
  - docker.example.com:5000/ubuntu:20.04
```

```
# 5000 포트에서 registry 도커 데몬 실행


$ docker run -d -p 5000:5000 --restart always --name registry registry:2

Unable to find image 'registry:2' locally
2: Pulling from library/registry
Digest: sha256:dc3cdf6d35677b54288fe9f04c34f59e85463ea7510c2a9703195b63187a7487
Status: Downloaded newer image for registry:2
69a4bfa70d4024e9f31a533f512c91062218c3f3832c2a4071733d9ab388f5dc

$ docker ps

CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS                            PORTS                    NAMES
69a4bfa70d40   registry:2   "/entrypoint.sh /etc…"   20 seconds ago   Up 19 seconds                     0.0.0.0:5000->5000/tcp   registry

# tag 변경

$ docker tag deepasmr_django localhost:5000/deepasmr_django
localhost:5000/deepasmr_django   latest    336db144cf0d   8 days ago    994MB

# push

$ docker push localhost:5000/deepasmr_django
Using default tag: latest
The push refers to repository [localhost:5000/deepasmr_django]
bafdbe68e4ae: Pushed
a13c519c6361: Pushed
latest: digest: sha256:ff1daa43da76127450466b032deb930d65087ca6ae52e3a3d50fc833edd7a03b size:3055

$ cd /var/lib/docker/volumes/ff1daa43da76127450466b032deb930d65087ca6ae52e3a3d50fc833edd7a03b/_data
docker

$ cd docker/registry/v2/repositories
deepasmr_django

```