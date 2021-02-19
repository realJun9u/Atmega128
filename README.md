# Atmega128
- [타이머/카운터](#timer-counter)
- [USART](#uart)
## Timer Counter 
- overflow interrupt : TCNTn값이 Max에서 1이 증가하면 0이 되고 이 때 Overflow Interrupt 발생  
- compare match interrupt : TCNTn가 OCRn값을 비교하여 같으면 Interrupt 발생  
#### 공통 레지스터  
1. TIMSK(Timer/Counter Interrupt Mask Register) : 타이머/카운터 0~2에서 발생하는 인터럽트를 개별적으로 허용하는 기능을 수행하는 레지스터  
![TIMSK](https://t1.daumcdn.net/cfile/tistory/999C8C3359C377F533)
> BIT 1 - OCIE0 (Output Compare Match Interrupt Enable) : T/C0 비교일치 인터럽트 활성화  
  BIT 0 - TOIE0 (Timer/Counter Overflow Interrupt Enable) :  T/C0 오버플로우 인터럽트 활성화  
2. TIFR(Timer/Counter Interrupt Flag Register) : 타이머/카운터 0~2에서 발생하는 인터럽트 플래그 저장  
![TIFR](https://t1.daumcdn.net/cfile/tistory/9957FC3359C37A921B)
> BIT 1 - OCF0 (Output Compare Match Flag) : TCNT0 = OCR0 경우 1로 set되고 인터럽트 요청  
  BIT 0 - TOV0 (Timer/Counter Overflow Flag) : 오버플로우 발생시 1로 set되고 인터럽트 요청  
3. TCNTn(Timer/Counter Register) : T/C0의 8,16비트 카운터 값을 저장하고 있는 레지스터  
4. OCRn(Timer/Counter Output Compare Register) : TCNT0와 비교하여 OC0 단자에 출력신호를 발생하기 위한 8,16비트 값을 저장하고 있는 레지스터  
### 타이머/카운터 0,2번  
- 8비트 계수기 (0~255 계수 가능, TCNTn에 저장)  
- 1개의 PWM출력(OCn)을 가진다.
#### 관련 레지스터  
1. TCCRn(Timer/Counter Control Register) : 동작모드 / 분주비 설정  
![TCCR0](https://t1.daumcdn.net/cfile/tistory/998BED3359C374FC1A)  
> BIT 7 - FOC0(Force Output Compare) : 강제로 OSC0 단자에 신호 출력 (인터럽트 X)  
  BIT 6,3 - WGM00,WGM01 (Waveform Generation Mode) : 파형 출력 모드  
  > Normal mode : 0 0 Overflow Interrupt에 사용  
    CTC mode(Compare Timer on Compare Match mode) : 1 0 Compare Match Interrupt에 사용  
    Fast PWM : 1 1 DC모터 세기, LED 밝기, 서보모터 등 듀티비를 이용하여 제어할 때 사용  
    Phase Correct PWM : 0 1 
  ![WGM](https://t1.daumcdn.net/cfile/tistory/990BE53359C3773326)  
  BIT 5,4 - COM01,COM00 (Compare Match Output Mode) : 비교매치 출력 모드  
  ![phase correct PWM](https://t1.daumcdn.net/cfile/tistory/992A473359CF36AD17)  
  ![Fast PWM](https://t1.daumcdn.net/cfile/tistory/99BEFA3359CC9DD724)  
  BIT 2,1,0 - CS02,CS01,CS00 분주비 설정 비트  
> <img src="https://t1.daumcdn.net/cfile/tistory/9923DE3359C366A00F" width="500" height="200">  
2. ASSR(Asynchronous Status Register) : T/C0가 외부클럭에 의한 비동기 모드로 동작하는 경우에 관련  
3. SFIOR(Special Function I/O Register)  
### 타이머/카운터 1,3번
- 16비트 계수기 (0 ~ 65535 계수 가능,TCNTn에 저장)  
- 3개의 PWM 출력과 1개의 캡쳐 기능을 가진다.
#### 관련 레지스터  
1. TCCRnA : 채널 A,B,C의 비교 출력 모드/ 파형 발생 모드 설정  
![TCCRnA](https://mblogthumb-phinf.pstatic.net/20160105_21/gwangryr_1451925127720LvTo1_PNG/1.PNG?type=w2)  
2. TCCRnB : 입력 캡쳐 설정/ 파형 발생 모드 설정/ 분주비 설정  
![TCCRnB](https://mblogthumb-phinf.pstatic.net/20160105_166/gwangryr_1451925870955bSqI7_PNG/2.PNG?type=w2)  
3. TCCRnC : 채널 A,B,C의 강제 출력 비교 설정  
![TCCRnC](https://mblogthumb-phinf.pstatic.net/20160105_134/gwangryr_1451926710526e5atN_PNG/3.PNG?type=w2)  
4. ICRn : 입력 캡쳐 신호에 의해 TCNT 값을 캡처하여 저장하는 레지스터  
5. ETIMSK : 타이머 1,3 인터럽트 관련  
6. ETIFR : 타이머 1,3 인터럽트 플래그 저장  

## UART 
- UART : Universal Asynchronous serial Reciever and Transmitter (비동기 직렬 통신)  
- USART : Universal Synchronous and Asyncronous serial Reciever and Transmitter (동기/비동기 직렬 통신)  
UART는 컴퓨터의 비동기 직렬 통신을 처리하는 프로그램으로서 보통 마이크로칩으로 실현되며, RS-232C 를 제공하여, 모뎀이나 기타 다른 직렬 장치들과 통신하거나 데이터를 주고받을 수 있게 하는 것이다. 예전에는 PC의 COM Port, 56Kbps 속도 이하 모뎀 등에서 이용되었으나 현재 PC에서는 USB가 그 뒤를 이어나가 대체되고 있는 실정이다. 하지만 아직까지는 막강한 표준력과 호환성을 자랑하여 MCU등에 표준적으로 사용하는 통신 방법이다.  
### 관련 레지스터
1. UDRn(Usart n I/O Data Register)  
![UDRn](https://cafeptthumb-phinf.pstatic.net/20130101_96/passionvirus_1357003211112hjmPX_JPEG/60-1_passionvirus.jpg?type=w740)  
> 송/수신되는 데이터 값을 저장하는 버퍼 역할. 
> TXBn/RXBn 레지스터로 구성되어 있는데, I/O 같은 주소에 공유된 형태로 구성되어 있으며 송/수신 이라는 다른 일을 하게 구성되어 있다.  
> 송신할 때는 TXBn의 데이터 전송  
> 수신할 때는 RXBn에 데이터 저장  
2. UCSRnA(Usart Control and Status Register A)  
![UCSRnA](https://cafeptthumb-phinf.pstatic.net/20130101_3/passionvirus_1357003211276DN3M6_JPEG/60-2_passionvirus.jpg?type=w740) 
> Bit 7 - RXCn(Recieve Complete)
>> 1 : UDRn의 수신버퍼에 읽지 않은 데이터가 존재하는 상태  
>> 0 : UDRn의 수신버퍼를 읽어서 비워져 있는 상태  

> Bit 6 - TXCn(Transmit Complete)
>> 1 : 송신 시프트 레지스터에 있는 송신데이터가 모두 전송되고 UDRn의 송신버퍼에 아직 송신데이터가 라이트되지 않은 상태  

> Bit 5 - UDREn(Usart Data Register Empty)
>> 1 : 송신 버퍼가 비어있어서 새로운 송신 데이터를 받을 준비가 된 상태  

> Bit 4 - FEn(Frame Error)
>> 1 : 수신할 때 프레임 에러가 발생한 상태
>> 프레임 에러 : 수신 문자의 첫 번째 스톱 비트가 0으로 검출되는 것  

> Bit 3 - DORn(Data Overrun Error)
>> 1 : 수신할 때 오버런 에러가 발생한 상태
>> 오버런 에러 : 수신버퍼에 읽지 않은 데이터가 들어있는 상태에서 수신 시프트 레지스터에 새로운 데이터가 수신 완료되고 다시 그 다음 수신 데이터인 3번째 데이터의 스타트 비트가 검출된 것  

> Bit 2 - UPEn(Usart Parity Error) 
>> 1 : 수신할 때 패리티 에러가 발생한 상태
>> 패리티 에러 : 정보의 전달 과정에서 오류가 생겼는지를 검사하기 위해 원래의 정보에 덧붙이는 비트를 패리티비트라하며 데이터비트와 패리티비트를 계산하여 짝수,홀수를 판별하여 에러인지 아닌지 구분한다.  

> Bit 1 - U2Xn(Double the Usart Transmission Speed)
>> 1 : 비동기 모드에서만 유효한 것으로 클럭의 분주비를 16에서 8로 절반만큼 낮추어 전송속도를 2배 높이는 기능  

> Bit 0 - MPCMn(Multi-Processor Communication Mode)
>> 1 : 멀티 프로세서 통신모드로 설정  

3. UCSRnB(Usart Control and Status Register B)  
![UCSRnB](https://cafeptthumb-phinf.pstatic.net/20130101_116/passionvirus_1357003211445fMSf0_JPEG/61-1_passionvirus.jpg?type=w740)  
> Bit 7 - RXCIEn(RX Complete Interrupt Enable)
>> 1 : 수신완료 인터럽트 활성화  

> Bit 6 - TXCIEn(TX Complete Interrupt Enable)
>> 1 : 송신완료 인터럽트 활성화  

> Bit 5 - UDRIEn(Usart Data Register Empty Interrupt Enable)
>> 1 : 송신 데이터 레지스터 준비완료 인터럽트 활성화  

> Bit 4 - RXENn(Reciever Enable)
>> 1 : 수신부 동작 활성화  

> Bit 3 - TXENn(Transmitter Enable)
>> 1 : 송신부 동작 활성화  

> Bit 2 - UCSZn2(Usart Character Size)
>> UCSRnC의 UCSZn1,0 비트와 함께 전송 문자의 데이터 비트 수 결정  

> Bit 1 - RXB8n(Recieve Data Bit 8)
>> 전송 데이터 수가 9로 설정된 경우 수신된 9번째 데이터 저장  

> Bit 0 - TXB8n(Transmit Data Bit 8)
>> 전송 데이터 수가 9로 설정된 경우 송신된 9번째 데이터 저장  

4. UCSRnC(Usart Control and Status Register C)  
![UCSRnC](https://cafeptthumb-phinf.pstatic.net/20130101_231/passionvirus_1357003211607U4RcT_JPEG/62-1_passionvirus.jpg?type=w740)  
> Bit 6 - UMSELn(Usart Mode Select)
>> 1 : 동기 전송 모드
>> 0 : 비동기 전송 모드  

> Bit 5,4 - UPMn1,UPMn0(Usart Parity Mode)
>> 0 0 : 패리티 체크 기능 사용 X
>> 1 0 : 짝수 패리티 방식 활성화
>> 1 1 : 홀수 패리티 방식 활성화  

> Bit 3 - USBSn(Usart Stop Bit Select)
>> 1 : 스탑비트 2개로 설정
>> 0 : 스탑비트 1개로 설정  

> Bit 2,1 - UCSZn1,UCSZn0(Usart Character Size)
>> 0 0 0 : 5bit
>> 0 0 1 : 6bit
>> 0 1 0 : 7bit
>> 0 1 1 : 8bit
>> 1 1 1 : 9bit  

> Bit 0 - UCPOLn(Usart Clock Polarity)
>> 동기 전송 모드의 슬레이브 동작에서만 사용됨
>> 1 : 송신데이터는 XCKn 클럭의 하강에지에서 새로운 값 출력,수신데이터는 상승에지에서 검출
>> 0 : 송신데이터는 XCKn 클럭의 상승에지에서 새로운 값 출력,수신데이터는 하강에지에서 검출  

5. UBRRnH/L(Usart Baud Rate Register)  
![UBRRn](https://cafeptthumb-phinf.pstatic.net/20130101_271/passionvirus_1357003211787rJjq4_JPEG/63-1_passionvirus.jpg?type=w740)  
> USART 송/수신 속도 설정
> UBRRnH - UBRRnL 순서로 라이트해야 한다.
> 통신속도는 표 참조  
![UBRR](https://cafeptthumb-phinf.pstatic.net/20130101_174/passionvirus_1357003211942CSt6R_JPEG/63-2_passionvirus.jpg?type=w740)


