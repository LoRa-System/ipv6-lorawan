# heltec ht-m01 게이트웨이 라즈베리파이에 연결하기

[ht-m01](https://heltec.org/project/ht-m01/)

[공식문서](https://heltec-automation-docs.readthedocs.io/en/latest/gateway/ht-m01/quick_start.html#use-ht-m01-with-linux-raspberry-pi)

USB 케이블로 raspberry pi와 gateway 연결한다. 이 때 케이블의 질이 좋아야 한다. 그렇지 않으면 연결이 잘 되지 않는 경우가 있었다. 

ht-m01 전용 packet-forwarder를 설치하자. packet-forwarder 프로그램은 엔드 디바이스로부터 온 로라 패킷을 udp로 네트워크 서버쪽의 브리지로 보낸다. 

```bash
mkdir lora
cd lora
sudo apt-get update
sudo apt-get install git
git clone https://github.com/Lora-net/picoGW_hal.git
git clone https://github.com/Lora-net/picoGW_packet_forwarder.git
git clone https://github.com/HelTecAutomation/picolorasdk.git
cd /home/pi/lora/picoGW_hal
make clean all
cd /home/pi/lora/picoGW_packet_forwarder
make clean all
cd /home/pi/lora/picolorasdk
chmod +x install.sh
./install.sh
#Run this script will create a service named "lrgateway". The purpose is to make the lora driver and data forwarding program run automatically at startup.

sudo cp -f /home/pi/lora/picolorasdk/global_conf_EU868.json /home/pi/lora/picoGW_packet_forwarder/lora_pkt_fwd/global_conf.json
#Put the configuration file on the specified path
```

```bash
nano /home/pi/lora/picoGW_packet_forwarder/lora_pkt_fwd/global_conf.json
```

![image](../assets/images/gatewayconf.png)

위의 파일로 이동해서 맨 밑에 gateway_conf 안쪽의 gateway_ID 16자리의 ID가 chirpstack네트워크 서버랑 일치해야한다. 위의 그림으로 따지면 3337323334004800을 [네트워크&앱 서버 게이트웨이 설정](../docs/lorawan_chirpstack.md)의 Chirpstack 애플리케이션 서버 페이지 설정하는 방법에서 Gateways 항목을 보면 Gateway ID가 있는데 여기에 써주면 된다. 

저 gateway_ID는 숫자 16자리면 어떤거든 상관없다. 네트워크&앱서버 사이트의 게이트웨이 id에 같은 값만 넣으면 연동된다. 


server_address를 자신의 ip 주소를 기입해주고(gateway, networkserver가 같은 라즈베리파이임), serv_port_up, serv_port_down을 1700으로 해준다. 

```bash
# 한번 재실행 해준다.
sudo systemctl restart lrgateway

# 상태가 정상인지 체크한다. 
sudo systemctl status lrgateway
```

HT-M01의 발열이 심하기 때문에 중간중간 `systemctl stop lorgateway` 후, 좀 쉬다가 `systemctl start lorgateway`하는 방식으로 진행하는게 좋다. 

