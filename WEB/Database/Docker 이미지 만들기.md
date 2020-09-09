# :whale: DOCKER 이미지 만들기

### 0. 도커 설치하기

- 나는 사이트에 가서 설치했음

[Get Started with Docker | Docker](https://www.docker.com/get-started)



### 1. 프로젝트 root에 Dockerfile 생성하기

<img width="165" alt="_2020-09-08__1 32 04" src="https://user-images.githubusercontent.com/60081217/92623175-e07c1800-f300-11ea-9cff-c1edc9f687ad.png">

Dockerfile, start 위치

- Dockerfile

```python
FROM python:3.6

RUN apt-get update \\
    && apt-get install -y --no-install-recommends \\
       postgresql-client \\
    && rm -rf /var/lib/apt/lists/*

COPY . /app 
RUN pip install -r /app/requirements.txt 
RUN chmod 755 /app/start 
WORKDIR /app  
EXPOSE 8000  

ENTRYPOINT ["/app/start"]
```

- start → 이건 쓰는 건가 마는 건가 잘 몰겠지만 혹시 몰라서 같이 넣었음

```python
#!/bin/bash

python manage.py makemigrations
python manage.py migrate

python manage.py runserver 0.0.0.0:8000
```



### 2. Docker build

- docker build 명령어를 입력한 후 `-t` 옵션을 넣으면 생성될 이미지의 이름(태그)를 지정할 수 있다.
- 나는 예제랑 똑같이 진행

```bash
docker build -t dockerdjango .
```

- 뒤에 `.` 은 현재 Dockerfile이 있는 경로이다.

- 와다다다ㅏ 진행한 후 → docker images 하면 방금 생성한 `dockerdjango:latest` 라는 이름의 이미지와, Base 이미지인 `python:3.6`이 있는 걸 볼 수 있음.

  <img width="616" alt="_2020-09-08__1 42 35" src="https://user-images.githubusercontent.com/60081217/92623181-e1ad4500-f300-11ea-9b0e-4fe03b94eaaa.png">

- 혹시 이때 <none> 이 뜬다면 뭐 잘못한거... 삭제하고 다시 해야함

```bash
docker rmi $(docker images -f "dangling=true" -q) 
# 이 명령어로 삭제 
```



### 3. Docker run

```bash
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

- 방금 생성한 이미지를 배포해보자.
- 8000포트를 열어주었기 때문에  `-p 8000:8000` 옵션을 넣어주자

```bash
docker run -d -p 8000:8000 --name dockerdjango dockerdjango:latest
```

| option                           | description                     |
| -------------------------------- | ------------------------------- |
| -d                               | 백그라운드 실행                 |
| -p <host port>:<containder port> | 호스트와 컨테이너의 포트를 매핑 |
| --name <containder name>         | 컨테이너의 이름을 지정          |
| <image name>:<image tag>         | 이미지의 이름과 버전을 지정     |

```bash
# 이걸로 마이그레이트 까지 해준다.
docker logs -f dockerdjango
```

<img width="682" alt="_2020-09-09__11 41 05" src="https://user-images.githubusercontent.com/60081217/92623184-e2de7200-f300-11ea-9460-b4bb5764a75d.png">

- 혹시 삭제해야 할 일이 생기면

```bash
# 컨테이너 리스트 확인
docker ps -a

# 컨테이너 삭제
docker rm [컨테이너id]

# 만약에 running중이라고 뜨면 중지 먼저하고 삭제
docker stop [컨테이너id]

# 컨테이너 모두 삭제
docker rm `docker ps -a -q`
```



### 4. docker hub에 업로드 하기

- 나만의 이미지를 다른 곳에서 사용할 수 있도록 `docker hub` 에 업로드 시키자!
- docker login

```bash
docker login [OPTIONS] [SERVER]
```

- docker tag

```bash
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

- docker push

```bash
docker push [OPTIONS] NAME[:TAG]
```

- 적용

```bash
# 유저 네임과 비번 치면 된다.
docker login

# 본인의 도커 허브에서 레퍼지토리를 만든다. -> 안만들어도 된다 ㄱ- 
docker tag dockerdjango:latest [docker username]/dockerdjango:latest

# 레퍼지토리로 푸시해준다.
docker push [docker username]/dockerdjango:latest
```

- 도커 허브

[Docker Hub](https://hub.docker.com/repository/docker/kyngskwk/test01)

<img width="934" alt="_2020-09-10__12 03 42" src="https://user-images.githubusercontent.com/60081217/92623192-e4a83580-f300-11ea-945c-6ffc4984045f.png">
<img width="747" alt="_2020-09-10__12 04 28" src="https://user-images.githubusercontent.com/60081217/92623197-e540cc00-f300-11ea-98de-ed84a9e2731f.png">

- 이렇게 올라간 걸 확인할 수 있다.



### 5. docker hub 이미지로 run하기

- 어디서든 hub에 업로드 된 이미지를 pull 받아서 사용할 수 있다.
- docker run 커맨드 시, loacl 저장소에 해당 image가 없다면, 자동으로 pull을 실행한다.

```bash
docker run -d -p 8000:8000 --name dockerdjango [docker username]/dockerdjango:latest
```

<img width="1025" alt="_2020-09-10__12 25 33" src="https://user-images.githubusercontent.com/60081217/92623200-e671f900-f300-11ea-9479-ccf406348e30.png">
<img width="1152" alt="_2020-09-10__12 28 37" src="https://user-images.githubusercontent.com/60081217/92623207-e7a32600-f300-11ea-8ab2-e45ad428714a.png">

- docker 컨테이너 접속

<img width="682" alt="_2020-09-10__12 42 54" src="https://user-images.githubusercontent.com/60081217/92623212-e8d45300-f300-11ea-8adf-d9ea94632671.png">

- 일단 docker로 서버를 돌리는 것은 성공

<img width="1141" alt="_2020-09-10__12 17 00" src="https://user-images.githubusercontent.com/60081217/92623223-ea9e1680-f300-11ea-8508-efa34db31479.png">

- 명령어 정리

```bash
# docker 컨테이너 확인
docker ps -a

# 컨테이너 삭제
docker rm [컨테이너id]

# 만약에 running중이라고 뜨면 중지 먼저하고 삭제
docker stop [컨테이너id]

# 모든 docker 컨테이너 삭제 
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

# 이미지 확인
docker images

# 이미지 삭제
docker rmi [이미지id]

# 강제 삭제
docker rmi -f [이미지id]

# 모든 docker 이미지 삭제
docker rmi $(docker images -q) 
```