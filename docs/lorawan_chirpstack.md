## chirpstack 오픈소스를 통해서 네트워크, 애플리케이션 서버환경 구축하기

Gateway Bridge, Network Server, Application Server 이렇게 3가지를 설치해서 네트워크 서버 전체를 완성합니다. Gateway Bridge는 게이트웨이의 로라 패킷을 네트워크 서버로 보내줄 때 데이터 포맷을 변환 해주는 다리역할을 합니다. 그렇게 네트워크 서버쪽으로 데이터가 오고 애플리케이션 서버를 만들어 관리자가 데이터를 쉽게 보거나 , MQTT 등 다양한 메시징 프로토콜로의 통합을 가능하게 합니다. 

라즈베리파이3이나 4에 chirpstack을 설치합니다. 

### Gateway Bridge

[설치 문서](https://www.chirpstack.io/gateway-bridge/)

Gateway Bridge, Network Server, Application Server 모두 왼쪽 카테고리의 Install → Requirement, Debian/Ubuntu를 설치하면 됩니다. 

```bash
# Requirements
sudo apt install mosquitto
```

```bash
# apt에 chirpstack을 설치할 수 있는 정보를 등록하고 apt update
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1CE2AFD36DBCCA00

sudo echo "deb https://artifacts.chirpstack.io/packages/3.x/deb stable main" | sudo tee /etc/apt/sources.list.d/chirpstack.list
sudo apt update
```

```bash
# bridge 설치
sudo apt install chirpstack-gateway-bridge
```

```bash
# 시작, 정지, 재시작, 로그
sudo systemctl [start|stop|restart|status] chirpstack-gateway-bridge
```

설정파일은 /etc/chirpstack-gateway-bridge/chirpstack-gateway-bridge.toml에 있습니다. 브리지는 설정파일을 바꾸지 않아도 됩니다. 

### Network Server

[설치 문서](https://www.chirpstack.io/network-server/)

```bash
# Requirements
sudo apt install mosquitto
sudo apt install postgresql
sudo apt install redis-server
```

```bash
# postgres 실행
sudo -u postgres psql
```

```bash
# create the chirpstack_ns user with password 'dbpassword'
create role chirpstack_ns with login password 'dbpassword';

# create the chirpstack_ns database
create database chirpstack_ns with owner chirpstack_ns;

# exit the prompt
\q
```

```bash
# 위에 chirpstack_ns user 만들어졌나 확인 비번 dbpassword 입력하면됨. 나올때는 \q
psql -h localhost -U chirpstack_ns -W chirpstack_ns
dbpassword
\q
```

```bash
# apt에 chirpstack을 설치할 수 있는 정보를 등록하고 apt update
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1CE2AFD36DBCCA00

sudo echo "deb https://artifacts.chirpstack.io/packages/3.x/deb stable main" | sudo tee /etc/apt/sources.list.d/chirpstack.list
sudo apt update
```

```bash
# 네트워크서버 설치
sudo apt install chirpstack-network-server
```

```bash
# 시작, 정지, 재시작, 로그
sudo systemctl [start|stop|restart|status] chirpstack-network-server
```

#### 설정 파일 수정
/etc/chirpstack-network-server/chirpstack-network-server.toml 파일을 수정해줘야함.

dsn 항목 찾아서 `dsn="postgres://chirpstack_ns:dbpassword@localhost/chirpstack_ns?sslmode=disable"`으로 수정. 

`sudo systemctl restart chirpstack-network-server`로 네트워크서버 재시작.

### App server

[설치 문서](https://www.chirpstack.io/application-server/)

```bash
# Requirements
sudo apt install mosquitto
sudo apt-get install postgresql
sudo apt-get install redis-server
```

```bash
# postgresql 키고 데이터베이스 생성
sudo -u postgres psql

# create the chirpstack_as user
create role chirpstack_as with login password 'dbpassword';

# create the chirpstack_as database
create database chirpstack_as with owner chirpstack_as;

# enable the trigram and hstore extensions
\c chirpstack_as
create extension pg_trgm;
create extension hstore;

# exit the prompt
\q
```

```bash
# 생성됐는지 확인
psql -h localhost -U chirpstack_as -W chirpstack_as
dbpassword
\q
```

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1CE2AFD36DBCCA00

sudo echo "deb https://artifacts.chirpstack.io/packages/3.x/deb stable main" | sudo tee /etc/apt/sources.list.d/chirpstack.list
sudo apt-get update
```

```bash
# 설치
sudo apt-get install chirpstack-application-server
```

```bash
# 시작, 정지, 재시작, 로그
sudo systemctl [start|stop|restart|status] chirpstack-application-server
```

#### 설정 파일 수정
/etc/chirpstack-network-server/chirpstack-application-server.toml 파일을 수정해줘야함.

dsn 항목 찾아서 `dsn="postgres://chirpstack_as:dbpassword@localhost/chirpstack_as?sslmode=disable"`으로 수정. 

bash창 아무대서나 `openssl rand –base64 32` 명령어를 입력하고 나오는 키값을 복사한뒤, 설정파일의 jwt_secret에 복사한 값 넣기. `jwt_secret="키값 복사한거 붙여넣기"`

`sudo systemctl restart chirpstack-application-server`로 네트워크서버 재시작.

