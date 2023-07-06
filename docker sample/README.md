# Ubuntu 20.04 에 Docker 설치

## 기본 패키지 설치
``` bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
```

## Docker에서 제공하는 패키지 저장소 등록

### 패키지 저장소를 사용하기 위한 GPG 인증키 다운로드
``` bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

### 패키지 저장소 경로 GPG 키와 함께 등록
``` bash
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 등록한 패키지 저장소 적용
``` bash
sudo apt-get update
```

### Docker 설치
``` bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 설치 후 확인
``` bash
docker -v
# Docker version 24.0.2, build cb74dfc
```

### Docker Token 등록
1. DC01 디렉토리로 이동
2. DC01 디렉토리에서 env 디렉토리 생성
3. env 디렉토리 안에 docker_token 파일 생성
4. [docker hub](https://hub.docker.com/settings/security) 사이트 접속
5. docker token 생성 후 발급된 token을 docker_token 에 기록 후 저장

#### Docker Volume 에서 웹서버 연결 경로 찾기
1. [docker hub](https://hub.docker.com/settings/security) 사이트 접속
  웹서버프로그램 이름 검색
 -v 옵션 사용시 경로 확인
 ```` bash
 $ docker run --name some-nginx -v /some/content:/usr/share/nginx/html -d nginx
 ````
2. Vagrant에서 찾기
```` bash
$ docker run -d nginx
 docker exec -it nginx_id /bin/bash   #nginx 실행
root@569ecc88ff8d:/# ls -la /etc
 drwxr-xr-x 1 root root    4096 Jul  4 17:24 nginx    #nginx 실행파일 찾기
root@569ecc88ff8d:/# ls -la /etc/nginx/    #nginx 실행파일 접속해서 nginx.conf 찾기
root@569ecc88ff8d:/# cat /etc/nginx/nginx.conf  #nginx 내용보기 -> 루트 경로 없음
 include /etc/nginx/conf.d/*.conf 
root@569ecc88ff8d:/# cat /etc/nginx/nginx.conf | grep "*root*" #추가 conf파일에서 root 포함한 내용찾기
root@569ecc88ff8d:/# ls -la /etc/nginx/conf.d/default.conf
location \ { root  /usr/share/nginx/html;}  #루트 경로 확인
````

* root 경로란 웹 주소창에 localhost를 검색했을 때 열리는 페이지 경로

실행결과 : Docker Volume에 있는 nginx 서버프로그램을 통해 localhost를 실행함
```` bash
$ docker run -d -p 8080:80 -v web_src_vol:/usr/share/nginx/html --name web-server1 nginx
````







