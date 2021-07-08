# LoRaWAN_151CC v1.0 예제 테스트

[heltec 사이트 참고](https://heltec-automation-docs.readthedocs.io/en/latest/stm32/lora_node_151/download_firmware.html)해서 예제 프로그램을 테스트합니다. 이미 프로그램들은 다 다운받아서 링크에 걸어놨습니다.

## 필요한 프로그램

[stm32cubeprogrammer](../assets/downloads/stm32cubeprogrammer.zip)
* firmware 업데이트할 때 사용. [참고](./ipv6_endevice.md)

[cubeide 다운](https://drive.google.com/file/d/17Eij9UC9eSBBQWbKmVsiL9ODc-DOLLgy/view?usp=sharing)
* 예제파일을 편집하고 flash 할 수 있는 툴

[예제 zip 파일](../assets/downloads/LoRaWAN_151CC_V1.0.zip)
* 예제 파일 소스코드

[stm32 st-link utility 프로그램 다운](../assets/downloads/stm32st-linkutility.zip)
* flash해서 엔드 디바이스에 들어간 코드들을 지울 때 사용하는 프로그램. [참고](./ipv6_endevice.md) 
* 지우개 버튼 누르면 flash로 내장된 프로그램이 지워진다.

## 테스트

### cubeide

cubeide를 키고 File → Open Project from File System...으로가서 예제 zip파일 압축 푼것을 프로젝트에 띄운다.

![image](../assets/images/lorawan_151cc/howtocubeide.png)

Inc 폴더의 Commissioning.h에서 LORAWAN_DEVICE_EUI, LORAWAN_APPLICATION_KEY만 애플리케이션 서버에 기입해주면된다. [참고](./ipv6_endevice.md)

위의 참고를 보고 stlink + 엔드디바이스로 컴퓨터에 엔드디바이스를 연결시킨뒤에 위에 그림의 2번 디버그 표시 버튼을 누르면 flash가 된다. 

![image](../assets/images/lorawan_151cc/howtocubeide2.png)

![image](../assets/images/lorawan_151cc/howtocubeide3.png)

flash를 하면서 디버깅을 위해서 엔드디바이스 안으로 들어가겠냐고(switch를 하겠냐고) 알림창이 2번 뜨는데 No를 해주면 된다.

![image](../assets/images/lorawan_151cc/howtocubeide4.png)

Download verified sucessfully가 뜨면서 엔드디바이스에 코드가 flash 완료된다. 

<br><br>

### 게이트웨이, 네트워크&앱서버

게이트웨이, 네트워크&애플리케이션 서버를 켜준다. [참고(게이트웨이)](./lorawan_gateway.md),  [참고(네트워크&앱서버)](./lorawan_chirpstack.md)

<br><br>

### uart usb, putty

위에서 ide로 flash할 때 썼던 stlink usb를 뽑고, uart usb를 연결시킨다. 컴퓨터 + uart usb + 엔드디바이스 형태. [참고](./ipv6_endevice.md)

![image](../assets/images/lorawan_151cc/putty.png)

putty를 키고 위의 그림대로 COM포트랑 속도 115200으로 맞춘뒤에 터미널에 접속한다. 디바이스 버튼 누를 때 [참고](./ipv6_endevice.md).

![image](../assets/images/lorawan_151cc/putty2.png)

lorawan(OTAA) 과정은 정상적으로 동작하지만 **앤드디바이스 터미널에서 출력이 안된다.**

<br><br>

### lorawan 과정 완료 후 애플리케이션 서버 캡처 사진들

**gateway 패킷 전체 캡쳐, join request&accept, data up&down**

![image](../assets/images/lorawan_151cc/gateway_all.png)

![image](../assets/images/lorawan_151cc/gateway_joinrequest.png)

![image](../assets/images/lorawan_151cc/gateway_joinaccept.png)

![image](../assets/images/lorawan_151cc/gateway_dataup.png)

![image](../assets/images/lorawan_151cc/gateway_datadown.png)

<br><br>

**application(enddevice) lorawan frames join request&accept, lorawan frames data up&down, device data**

![image](../assets/images/lorawan_151cc/application_lorawanframes_joinrequest.png)

![image](../assets/images/lorawan_151cc/application_lorawanframes_joinaccept.png)

![image](../assets/images/lorawan_151cc/application_lorawanframes_dataup.png)

![image](../assets/images/lorawan_151cc/application_lorawanframes_datadown.png)

![image](../assets/images/lorawan_151cc/application_devicedata_join_up.png)