# chirpstack 오픈소스를 통해서 네트워크, 애플리케이션 서버환경 구축 및 설정

Gateway Bridge, Network Server, Application Server 이렇게 3가지를 설치해서 네트워크 서버 전체를 완성합니다. Gateway Bridge는 게이트웨이의 로라 패킷을 네트워크 서버로 보내줄 때 데이터 포맷을 변환 해주는 다리역할을 합니다. 그렇게 네트워크 서버쪽으로 데이터가 오고 애플리케이션 서버를 만들어 관리자가 데이터를 쉽게 보거나 , MQTT 등 다양한 메시징 프로토콜로의 통합을 가능하게 합니다. 

라즈베리파이3이나 4에 chirpstack을 설치합니다. 

<br>

## Gateway Bridge

[설치 문서](https://www.chirpstack.io/gateway-bridge/)

Gateway Bridge, Network Server, Application Server 모두 왼쪽 카테고리의 Install → Requirement, Install → Debian/Ubuntu를 설치하면 됩니다. 

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

<br>

## Network Server

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

### 설정 파일 수정
/etc/chirpstack-network-server/chirpstack-network-server.toml 파일을 수정해줘야함.

dsn 항목 찾아서 `dsn="postgres://chirpstack_ns:dbpassword@localhost/chirpstack_ns?sslmode=disable"`으로 수정. 

`sudo systemctl restart chirpstack-network-server`로 네트워크서버 재시작.

<br>

## App server

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

### 설정 파일 수정
/etc/chirpstack-application-server/chirpstack-application-server.toml 파일을 수정해줘야함.

dsn 항목 찾아서 `dsn="postgres://chirpstack_as:dbpassword@localhost/chirpstack_as?sslmode=disable"`으로 수정. 

bash창 아무대서나 `openssl rand –base64 32` 명령어를 입력하고 나오는 키값을 복사한뒤, 설정파일의 jwt_secret에 복사한 값 넣기. `jwt_secret="키값 복사한거 붙여넣기"`

`sudo systemctl restart chirpstack-application-server`로 네트워크서버 재시작.

<br>

## Chirpstack 애플리케이션 서버 페이지 설정하는방법

아이디 : admin 

비밀번호 : admin

왼쪽 탭의 카테고리를 기준으로 설명합니다. 

### Network-servers

![image](../assets/images/chirpstack-network-servers.png)


### Gateway-profiles

![image](../assets/images/chirpstack-gateway-profiles.png)

오른쪽 하단에 ADD EXTRA CHANNEL을 누르게 되면 화면처럼 추가 input이 뜨게 된다. 

왼쪽 카테고리의 Organizations, All users, API keys, Org.dashboard, Org. users, Org. API keys는 그대로 두고 service-porfiles로 갑니다. 

### Service-profiles

![image](../assets/images/chirpstack-service-profiles.png)


### Device-profiles

![image](../assets/images/chirpstack-device-profiles.png)

오른쪽 탭의 JOIN(OTAA/ABP), CLASS-B, CLASS-C 부분의 체크탭에 체크만 해준다. 칸은 채울 필요없다. 

### Gateways 

![image](../assets/images/chirpstack-gateways.png)

Gateway name, Gateway description을 원하는대로 써주고, Gateway ID를 [global_conf.json](./lorawan_gateway.md)에 썼던 gateway id를 써주면 된다. 16자리 숫자 아무거나 네트워크 서버랑 게이트웨이쪽 json 파일이랑 일치만 하면 연동된다. 화면에는 33 37 ... 으로 써져 있지만, 본인의 glbal_conf.json에서 게이트웨이를 확인하고 그걸 써주면 된다. 

### Applications

![image](../assets/images/chirpstack-applications.png)


### Applications

![image](../assets/images/chirpstack-applications.png)


### Applications으로 들어가서 Device 부분

![image](../assets/images/chirpstack-applications-devices.png)

Device name, Device description을 원하는대로 써주고, Device EUI를 [Commissioning.h](https://github.com/aenrbes/IPv6-over-LoRaWAN/blob/master/arch/cpu/loramac/mac/Inc/Commissioning.h)의 LORAWAN_DEVICE_EUI랑 같게 해주면 된다. 꼭 저 키 값을 적어줘야 하는것이 아니고 예를 들어, Commissioning.h의 LORAWAN_DEVICE_EUI를 { 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00 }로 바꿨다면, 사이트쪽 Device EUI도 00, 00, 00, 00, 00, 00, 00, 00으로 적으면 연동된다. 오른쪽 부분에 MSB인 상태에서 적어야한다. 

### Device 생성한뒤 3번째 탭 KEYS(OTAA)

![image](../assets/images/chirpstack-applications-devices-otaa.png)

[Commissioning.h](https://github.com/aenrbes/IPv6-over-LoRaWAN/blob/master/arch/cpu/loramac/mac/Inc/Commissioning.h)의 LORAWAN_APPLICATION_KEY랑 같게 해주면 된다. 위에 LORAWAN_DEVICE_EUI처럼 다른 값으로 바꾸더라도 헤더 파일과 chirpstack의 값과 일치하기만 하면 연동된다.

이렇게 엔드디바이스에서 Commissioning.h의 2가지를 사이트와 일치시키기만 하면 연동된다. 나머지는 바꿀 필요 없다. 