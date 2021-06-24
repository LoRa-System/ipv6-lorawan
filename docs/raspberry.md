# 라즈베리파이 ssh 연결 및 해당 프로젝트를 진행하기 위한 spi 설정

1. [win32diskmanager 다운](https://sourceforge.net/projects/win32diskimager/files/latest/download)
2. 32기가 이상인 sd카드를 컴퓨터와 연결
3. win32diskmanager 실행 
4. 이 [링크](https://www.raspberrypi.org/software/operating-systems/)의 Pi OS Lite를 받고, 압축을 풀어서 .img파일을 win32diskmanager를 사용해 sd카드에 주입. 
5. 주입된 sd카드에 ssh라는 이름으로 빈파일 생성. [참고](https://kkamikoon.tistory.com/149)
6. 공유기에 라즈베리파이를 랜선 연결후에 공유기 설정으로 가서 라즈베리파이의 ip주소를 얻어서 putty로 접속. 포트 22번, 아이디 pi, 비밀번호 raspbery. 

접속하면 먼저 

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

spi를 가능하게 설정한다. 

```bash
sudo raspi-config
```

interfacing options에서 spi로 가서 yes로 설정한다. 

