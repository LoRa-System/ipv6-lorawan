# border-router 설치 과정

라즈베리파이3이나 4 환경에서 border-router를 설치합니다. 

<br>


[설치 문서](https://github.com/aenrbes/ipv6-over-LoRaWan)의 howto 4번



```bash
sudo git clone https://github.com/aenrbes/ipv6-over-LoRaWan.git

#mosquitto.h 사용을 위한 libmosquitto-dev 설치
sudo apt-get install libmosquitto-dev

#로컬 상에서 mqtt 사용을 위한 IP 수정 
sudo nano /home/pi/ipv6-over-LoRaWan/os/services/lorawan-border-router/native/slip-trx.c
```

![image](https://github.com/LoRa-System/ipv6-lorawan/blob/master/assets/images/border-router_before.png)

위 사진의 mosquitto_connect 인자 값 “10.42.0.63” -> “사용하는 App server 로컬 주소” ex) “192.168.100.156”으로 변경

![image](https://github.com/LoRa-System/ipv6-lorawan/blob/master/assets/images/border-router_after.png)

```bash
sudo make

#border-router 실행
sudo ./border-router-native -a * fd00::1/64
```
![image](https://github.com/LoRa-System/ipv6-lorawan/blob/master/assets/images/border-router_appserver.png)

→ * 은 App server에 등록된 application id의 인덱스를 확인하고 넣어준다.<br>
ex) sudo ./border-router-native -a 8 fd00::1/64 <br>


실행 결과 tun0 인터페이스가 성공적으로 올라온 것을 확인한다.

![image](https://github.com/LoRa-System/ipv6-lorawan/blob/master/assets/images/border-router_result.png)
