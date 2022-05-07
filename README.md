## 도커 기본 명령어
---
#### 도커 로그인
```
docker login
```

#### 사용자 권한 추가 (sudo 없이 도커 명령어 실행하기)

```
sudo usermod -aG docker $USER
logout
```

#### 도커 이미지 검색
- d로 시작하는 이미지 모두 출력

```
docker search [옵션] <이미지이름:태그명>

$ docker images d*
REPOSITORY            TAG       IMAGE ID       CREATED       SIZE 
deepasmr_django       latest    336db144cf0d   8 days ago    994MB
drf_tutorial_django   latest    cb3899be2008   10 days ago   974MB

```

#### 도커 이미지 다운로드
```
docker pull [옵션] <이미지이름:태그명>
```

#### 도커 이미지 삭제
```
docker rmi [옵션] <이미지이름>
```

#### 도커 이미지 목록 출력
```
docker images
```
- `--no-truc` : 이미지명 풀네임으로 보이기

#### 도커 컨테이너 생성

```
docker create --name webserve nginx:1.14
```

#### 도커 이미지 태그명 변경

```
docker image tag [before tag] [after tag]
```


#### 도커 레지스트리에서 nginx 이미지 검색
```
docker search nginx
```

#### 도커 레지스트리에서 nginx 최신 버전 이미지 다운

```
docker pull nginx:latest
```

#### 이미지 삭제
```
docker rmi [옵션] <이미지이름>
```

#### web이라는 컨테이너로 ginx 이미지를 받아 80 포트로 도커 데몬을 백그라운드(-d)로 실행
```
docker run -d --name web -p 80:80 nginx:latest
```

#### 도커 시작/중지
```
docker start web
docker stop web
```

### 도커 프로세스 확인
```
docker ps

# 실행중인 도커 프로세스 종료
alias sp='docker rm -f $(docker ps -aq)'

$sp
6cbb8b9770a
...
...
```


#### 도커 허브에 컨테이너 업로드 

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
TODO


registry에 저장된 volume 확인


#### 컨테이너 명령어 실행하기(exec)
- run은 새로운 컨테이너를 만들어서 실행하는 것,
- exec는 실행중인 컨테이너에 명령어를 내리는 것

```
docker exec [옵션] 컨테이너 명령어 

$ docker exec -it webserver /bin/bash
```
  - i: interative
  - t: terminal

#### 도커 로그 확인

```
docker logs [options] 컨테이너id
```

-f : 실시간 로그 확인

-tail 10 : 마지막으로 볼 raw 수

#### 포그라운드로 실행중인 컨테이너에 연결

```
docker attach [옵션] 컨테이너 이름
```

#### 컨테이너에서 동작되는 프로세스 확인
```
docker top
```

#### 이미지의 설정 확인
```
docker inspect webserver

docker inspect --format '{{.NetworkSettings.IPAddress}}' webserver
```

---
## 컨테이너 관리

- 컨테이너 하드웨어 리소스를 어떻게 제한하는지
  
  ```
  도커 Command를 통해 제한할 수 있는 리소스
  1. CPU
  2. Memory
  3. Disk I/O

  docker run --help
  ```
- 컨테이너 사용 리소스를 확인하는 모니터링 툴



#### Memory 리소스 제한

- 시스템 가용 메모리를 확인하는게 중요!!
- 제한 단위는 b, k, m , g (byte, kb, mb, gb)


nginx 컨테이너가 사용할 최대 메모리를 512m로 설정
```
docker run -d -m 512m nginx:1.14
```

nginx 컨테이너가 사용할 메모리가 1g이고 500m 보다는 크도록 설정
```
docker run -d -m 1g --memory-reservation 500m nginx:1.14
```

컨테이너가 사용할 스왑 메모리 영역에 대한 설정
(메모리 + 스왑)

- 생략 시 메모리의 2배가 설정
```
# 200m 메모리
# memory-swap: 300m
# swap memory: 100m
# default 400m (200m * 2)

docker run -d -m 200m --memory-swap 300m nginx:1.14
```

OOM Killer가 프로세스를 kill 하지 못하도록 보호한다. 
```
docker run -d -m 200m --oom-kill-disable nginx:1.14
```

```
$cat /sys/fs/cgroup/memory/docker/이미지ID/memory.oom_control

oom_kill_disable=1
under_oom 0
oom_kill 0
```


---

### CPU 리소스 제한

컨테이너에 할당할 CPU 코어 수를 지정
  - `--cpus="1.5"` 컨테이너가 최대 1.5개의 CPU 파워사용가능
```
docker run -d --cpus="1.5" ubuntu:1.14
```

컨테이너가 사용할 수 있는 cpu나 코어를 할당
  
  - `cpu index`는 0부터, --cpuset-cpus=0-4 
```
docker run -d --cpu-shares 2048 ubuntu:1.14
```

컨테이너가 사용하는 CPU 비중을 1024 값을 기반으로 설정
  - `--cpu-share 2048` 
  - 기본 값보다 두 배 많은 CPU 자원을 할당
```
docker run -d --cpuset-cpus 0-3 ubuntu:1.14
```

```
docker run --cpuset-cpu 1 --name c1 -d stress:latest stress --cpu 1
```

---
Block I/O 제한

`--blkio-weight`, `--blkio-weight-device` : 
 Block IO의 Quota를 설정할 수 있으며 100~1000까지 선택 defaul값은 500

`--device-read-bps`, `--device-write-bps` : 특정 디바이스에 대한 읽기와 쓰기 작업의 초당 제한을 kb, mb, gb 단위로 설정

`--device-read-iops`, `--device-write-iops` : 컨테이너의 read/write 속도의 쿼터를 설정한다. 초당 quota를 제한해서 I/O를 발생시킨다. 0 이상의 정수로 표기, 초당 데이터 전송량 = IOPS * 블럭 크기(단위 데이터 용량)


```
docker run -it --rm --blkio-weight 100 ubuntu:latest /bin/bash
```

```
docker run -it --rm --device-write-bps /dev/vda:1mb ubuntu:latest /bin/bash
```

```
docker run -it --rm --device-write-bps /dev/vda:1mb ubuntu:latest /bin/bash
```

```
docker run -it --rm --device-write-bps /dev/vda:10mb ubuntu:latest /bin/bash
```

```
docker run -it --rm --device-write-bps /dev/vda:10 ubuntu:latest /bin/bash
```

```
docker run -it --rm --device-write-bps /dev/vda:100 ubuntu:latest /bin/bash
```


---
### 모니터링


#### Docker monitoring commands
- `docker stats` : 실행중인 컨테이너 런타임 통계를 확인

    ```
    docker stats [OPTIONS] [CONTAINER]
    ```
  
- `docker event` : 도커 호스트의 실시간 event 정보를 수집해서 출력
  
  ```
  docker event -f container=<NAME>
  docker image -f container=<NAME>
  ```

- [cAdvisor](https://tech.socarcorp.kr/data/2020/03/10/ml-model-serving.html)
  - 구글에서 공개한 docker 모니터링 애플리케이션

---

### 부하 컨테이너 생성

- 컨테이너 빌드: 부하 테스트 프로그램 `stress`를 설치하고 동작시키는 컨테이너 빌드

- CPU 부하 테스트: 프로세스 수 2개와 사용할 메모리만큼 부하 발생: `stress --cpu 2`

- 메모리 부하 테스트: 프로세스 수 2개와 사용할 메모리만큼 부하 발생: `stress --vm 2 --vm-bytes <사용할 크기>`
 
```
$ vi Dockerfile
FROM debian
LABEL email="ithingvvv@gmail.com"
LABEL name="ithingvvv" 
RUN apt-get update; apt-get install stress -y
CMD ["/bin/sh", "-c", "stress -c 2"]

# stress 이미지 빌드

docker build -t stress .
```

### 메모리 리소스 제한
swap 메모리 용량 제한이 실제 메모리 제한과 어떤 관련성이 있는지 확인하기

```
# 5초동안 부하테스트 진행

# 성공
docker run -m 100m --memory-swap 100m stress:latest --vm 1 --vm-bytes 90m -t 5s

# 실패(메모리부족)
docker run -m 100m --memory-swap 100m stress:latest stress --vm 1 --vm-bytes 130m -t 5s

# 성공 
docker run -m 100m stress:latest stress --vm 1 --vm-bytes 150m -t 5s

```

### CPU 사용량 조절
```
# 컨테이너별로 CPU 상대적 가중치를 할당하여 실행되도록 구성한다.

$ docker run -c 2048 --name cloud1 -d stress:latest
30951d141cf51cd2e8d83de33fd5b5e1bab5399d11b1ac2f95792bb9afaa2b2f

$ docker run --name cloud2 -d stress:latest
0624536185c729b6b572685fb7e5db47a5856b7911c4df58269ebe0e4825a468

$ docker run -c 512 --name cloud3 -d stress:latest
b014d97e11878b3f3ac41196ac48fd4bda1978cd8452c3864b2c0e39dc280af7

$ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                    NAMES
b014d97e1187   stress:latest   "/bin/sh -c 'stress …"   12 seconds ago   Up 8 seconds                             cloud3
0624536185c7   stress:latest   "/bin/sh -c 'stress …"   42 seconds ago   Up 40 seconds                            cloud2
30951d141cf5   stress:latest   "/bin/sh -c 'stress …"   58 seconds ago   Up 57 seconds                            cloud1

```

```
docker stats cloud1 cloud2 cloud3

# cloud1이 가장 많은 리소스를 사용한다.

CONTAINER ID   NAME       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O          BLOCK I/O   PIDS
b014d97e1187   cloud3     84.00%    732KiB / 6.091GiB     0.01%     796B / 0B        0B / 0B     4   
0624536185c7   cloud2     133.36%   704KiB / 6.091GiB     0.01%     866B / 0B        0B / 0B     4   
30951d141cf5   cloud1     176.65%   700KiB / 6.091GiB     0.01%     866B / 0B        0B / 0B     4   
```


### Block I/O 제한

-  `lsblk` : 

컨테이너에서 `--device-wrtie-iops` 를 적용해서 `write` 속도의 초당 `quota`를 제한해서 `IO write` 를 발생시킨다.

```
$ docker run -it --rm --device-write-iops /dev/xvda:10 ubuntu:latest /bin/bash

hoon@57f8b6fb1ff8: /# dd if=/dev/zero of=file1 bs=1M count=10 oflag=direct

10+0 records in
10+0 records out
```

----

### 컨테이너 볼륨