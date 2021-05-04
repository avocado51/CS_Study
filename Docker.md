# Docker
### 도커 설치 
```
$ sudo apt update -y
$ sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
$ sudo apt update -y
$ apt-cache policy docker-ce
$ sudo apt install -y docker-ce
```

- 설치 확인 
```
$ sudo systemctl status docker
```

- 도커 이미지 생성 및 빌드 
```
$ 'dockerfile 생성'
$ docker build -t [dockerfilename] .
$ docker image ls | grep [dockerfilename]
```
##
### docker-compose?
- 독립된 개발 환경을 빠르게 구성할 수 있다.
- 컨테이너 실행에 필요한 옵션을 docker-compose.yml 파일에 적어둘 수 있고 컨테이너 간 실행 순서나 의존성을 관리할 수 있다.
- 도커 엔진의 버전 >= 1.13.1
- 도커 컴포즈 버전 >= 1.6.0
- docker-compose 설치 
```
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```
- 도커 컴포즈 실행 
```
$ docker-compose up -d 
	- docker-compose.yml 파일의 내용에 따라 이미지를 빌등하고 서비스를 진행한다. 
		- 서비스를 띄울 네트워크 설정 
		- 필요한 볼륨 설정
		- 필요한 이미지 풀 
		- 필요한 이미지 빌드 
		- 서비스 의존성에 따라 서비스 실행
	- up -d : 서비스 실행 후 콘솔로 빠져나옴
	- up --force-recreate : 컨테이너를 지우고 새로 만든다. 
	- up --build : 서비스 시작 전 이미지를 새로 생성 
```
##
### docker-compose 명령어
  > $ docker-compose
  - rm : docker-compose로 생성한 컨테이너들을 일괄 삭제. 삭제 전, 관련 컨테이너들을 종료해야 한다. 
  - ps : 현재 환경에서 실행 중인 각 서비스의 상태를 보여준다. 
  - stop / start 
  - down : 서비스를 지운다. 
  - --volume : 볼륨까지 삭제하낟. 
  - exec : 실행중인 컨테이너에 명령어 실행
  - logs : 서비스 로그 확인. logs [service name] 하면 특정 서비스의 로그를 볼 수 있다. 
    - -f : 로그 실시간 확인 
  - run : docker-compose up 명령어를 통해 생성 및 실행된 컨테이너에서 임의의 명령을 실행하기 위해 사용한다. 
	- $docker-compose run [서비스명] [명령]
	- e.g) $docker-compose run redis /bin/bash

### 도커 alias
```
alias dco='docker-compose'
alias dcb='docker-compose build'
alias dce='docker-compose exec'
alias dcps='docker-compose ps'
alias dcr='docker-compose run'
alias dcup='docker-compose up'
alias dcdn='docker-compose down'
alias dcl='docker-compose logs'
alias dclf='docker-compose logs -f'
```
