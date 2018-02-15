# README #

##### 본 문서에는 sandwhichi Docker Service의 구동법이 명세되어있습니다

### 서비스 실행법 ###

> Docker 설치, 세팅 방법에 대한 설명은 제외되어있습니다.

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
# For local
COMPOSE_FILE=docker-compose.yml:./env/local.yml
# For Developement Server
# COMPOSE_FILE=docker-compose.yml:./env/dev.yml
# For Production Server
# COMPOSE_FILE=docker-compose.yml:./env/prod.yml

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
