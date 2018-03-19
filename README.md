# README #

##### 본 문서에는 Sandwhichi Docker Master 구동법이 명세되어있습니다

### 서비스 실행법 ###

> Docker 설치, 세팅 방법에 대한 설명은 제외되어있습니다.

### Information ###
docker master를 이용해서 front-end 서버와 api를 띄우는 경우, 종종 api 내에서 일부 디렉토리가 생성되지 않아 제대로 실행되지 않을 때가 있다. 그럴 땐 다음과 같이 해결하면 된다. 일단 docker image가 build까지 되어 컨테이너를 띄운 상태라는 가정하에 기술한다. 
```bash
# 실행되어 있는 api 컨테이너로 접속한다 
$ docker exec -it sdw_api_1 /bin/bash

# storage 안으로 들어가 Laravel 관련 디렉토리를 생성시켜준다.
$ cd storage
$ mkdir framework

$ cd framework
$ mkdir sessions views cache
```

#### .env 파일생성
> 운영 환경에 따라 다른 커맨드를 요구합니다. 프로젝트명을 지정하거나, 필요에 따라 환경별 yml파일을 머지해야하는데,
> 번거롭기 때문에 .env파일을 작성하여 보다 쉽게 운영합니다.
```sh
$ cp .env.example .env
```
> .env 파일의 "#" 으로 해당 Line을 주석 처리할 수 있습니다.
> 필요한 운영 환경의 COMPOSE_FILE만 남기고 나머지 COMPOSE_FILE들은 삭제하거나, 주석처리합니다.

- 로컬호스트에서의 .env 예제
```sh
# For local, without projects
COMPOSE_FILE=docker-compose.yml:./env/local.yml
# For local, with projects
# 아래의 설정대로 실생시키고 싶은 경우, 상대경로 ../api와 ../frontend에 프로젝트가 존재하는지 확인하세요.
COMPOSE_FILE=docker-compose.yml:./env/local.yml:../api/docker-compose.yml:../frontend/docker-compose.yml
# For Developement Server
# COMPOSE_FILE=docker-compose.yml:./env/dev.yml
# For Production Server
# COMPOSE_FILE=docker-compose.yml:./env/prod.yml

# 운영환경에 맞게 APP_ENV를 설정합니다. local, dev, prod 중에 선택하세요
APP_ENV=local

# 프로젝트가 실행될 환경의 도메인 주소를 설정합니다.
API_DOMAIN_NAME=local.api.sandwhichi.com
FRONT_DOMAIN_NAME=local.sandwhichi.com

# Docker master가 하위 프로젝트를 include하기위해, docker-compose.yml 파일이 존재하는 디렉토리를 설정해주세요
API_DOCKERFILE_DIR=../api
FRONT_DOCKERFILE_DIR=../frontend

# 가상머신 속에서 프로젝트의 위치를 절대 경로로 지정합니다.
# API project의 경우 /var/www/api로 고정되어있습니다.
API_PROJECT_ROOT_PATH=/var/www/api

# Sentry
# Sentry를 설치하고 싶은 경우 아래 값을 모두 입력하세요.
SENTRY_SECRET_KEY=
SENTRY_SERVER_EMAIL=
SENTRY_EMAIL_HOST=
SENTRY_EMAIL_PORT=
SENTRY_EMAIL_USER=
SENTRY_EMAIL_PASSWORD=
SENTRY_EMAIL_USE_TLS=

# Bitbucket Credential
# Bitbucket에서 프로젝트를 가져오는 entrypoint를 가지고있다면 하단 정보를 모두 입력하세요.
BITBUCKET_TEAM_NAME=lubycon
BITBUCKET_USERNAME=
BITBUCKET_PASSWORD=

```

### 커맨드 목록
> 모든 docker-compose 명령어는 docker-compose파일이 존재하는 디렉토리에서만 실행 할 수 있습니다.

#### Docker-master first build
> Docker-master를 다운받고, env를 설정한 직후에는 up을 할 수 없습니다.
> build를 먼저 실행해주세요. 처음 한번만 실행하면 됩니다.
```sh
$ docker-compose build
```

#### 서비스 생성
> 서비스의 컨테이너가 존재하지 않을때 실행합니다.
> 이미 컨테이너가 있다면 이미지에 의존하여 재생성합니다.
```sh
$ docker-compose up
```

#### 서비스 삭제
> 서비스의 컨테이너를 모두 제거합니다.
> Local환경의 경우 컨테이너를 제거하면 데이터베이스의 내용이 모두 사라집니다
```sh
$ docker-compose down
```


#### 서비스 업데이트시
> docker-compose.yml이 업데이트 되었고, 적용하고싶을때 실행합니다.
> 기존 이미지를 사용하지 않고, dockerfile대로 이미지를 재생성합니다.
```sh
$ docker-compose up --build
```

#### 서비스 중단을 하고싶을때
> 서비스가 실행중인데 일시적으로 중단하고 싶을때.
> 이미지와는 관계없는 작업이며 컨테이너의 상태만이 변경됩니다.
```sh
$ docker-compose stop
```

#### 중단된 서비스를 재 실행하고 싶을 때.
> 서비스가 실행중인데 일시적으로 중단하고 싶을때.
> 이미지와는 관계없는 작업이며 컨테이너의 상태만이 변경됩니다.
```sh
$ docker-compose start
```


#### 컨테이너와 관계없이 이미지만 빌드하고싶을때
> dockerfile은 업데이트 되었지만, 서비스에 반영하고싶지 않고, 정상적으로 빌드 되는지 테스트하고싶을때 사용
> 빌드가 실패하더라도 컨테이너에 영향을 주지 않습니다.
> 기본적으로 한번 실행했던 커맨드는 캐싱되어 빠르게 넘어갑니다.
> 캐시에 의존하지 않고 빌드하고싶다면 --no-cache를 옵션으로 입력합니다.
```sh
$ docker-compose build
$ docker-compose build --no-cache
```
