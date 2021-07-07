## heltec에서 제공하는 예제 파일중 한개를 테스트합니다.

[heltec 사이트 참고](https://heltec-automation-docs.readthedocs.io/en/latest/stm32/lora_node_151/download_firmware.html)해서 예제 프로그램을 테스트합니다. 이미 프로그램들은 다 다운받아서 링크에 걸어놨습니다.

### 필요한 프로그램

[cubeide 다운](../assets/downloads/cubeide.exe.zip)(예제파일을 편집하고 flash 할 수 있는 툴)

[예제 zip 파일](../assets/downloads/LoRaWAN_151CC_V1.0.zip)

[stm32 st-link utility 프로그램 다운](../assets/downloads/stm32st-linkutility.zip)(flash한걸 지울때 사용. [이것을 참고해서](./ipv6_endevice.md) 연결하고 지우개 버튼 누르면 flash로 내장된 프로그램이 지워진다.)

### 테스트

cubeide를 키고 File → Open Project from File System...으로가서 예제 zip파일 압축 푼것을 프로젝트에 띄운다.

![image](../assets/images/lorawan_151cc/howtocubeide.png)

Inc 폴더의 Commissioning.h에서 LORAWAN_DEVICE_EUI, LORAWAN_APPLICATION_KEY만 애플리케이션 서버에 맞춰주면된다. [참고](./ipv6_endevice.md)

다음 위의 그림의 2번 디버그 표시된 버튼을 누르면 flash가 된다. flash가 완료되면 IDE 밑의 콘솔에 verified 라고 뜨면서 완료가 된다.

flash를 하면서 엔드디바이스 안으로 들어가겠냐고(switch를 하겠냐고) 알림창이 뜨는데 No를 해주면 된다.(2번 정도뜸)

게이트웨이, 네트워크 서버, 애플리케이션 서버를 작동시키고