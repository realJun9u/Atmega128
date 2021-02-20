# Atmega128
- [타이머/카운터](#timer-counter)
- [USART](#uart)
- [ADC](#ad-convertor)
- [SPI](#spi)
- [I2C](#i2c)
## Timer Counter 
- overflow interrupt : TCNTn값이 Max에서 1이 증가하면 0이 되고 이 때 Overflow Interrupt 발생  
- compare match interrupt : TCNTn가 OCRn값을 비교하여 같으면 Interrupt 발생  
### 공통 레지스터  
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
### 관련 레지스터  
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
### 관련 레지스터  
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
- UART는 컴퓨터의 비동기 직렬 통신을 처리하는 프로그램으로서 보통 마이크로칩으로 실현되며, RS-232C 를 제공하여, 모뎀이나 기타 다른 직렬 장치들과 통신하거나 데이터를 주고받을 수 있게 하는 것이다. 예전에는 PC의 COM Port, 56Kbps 속도 이하 모뎀 등에서 이용되었으나 현재 PC에서는 USB가 그 뒤를 이어나가 대체되고 있는 실정이다. 하지만 아직까지는 막강한 표준력과 호환성을 자랑하여 MCU등에 표준적으로 사용하는 통신 방법이다.  
- UART : Universal Asynchronous serial Reciever and Transmitter (비동기 직렬 통신)  
- USART : Universal Synchronous and Asyncronous serial Reciever and Transmitter (동기/비동기 직렬 통신)  
#### USART 특징  
- 동기모드    : Master(내부클럭), Slave(외부클럭:XCKn)
- 비동기모드  : 내부클럭사용
- 전이중 통신
- 멀티프로세서 통신
- 높은 정밀도의 보레이트발생기 내장
- 전송데이터 5~9비트 설정
- 스톱비트 1~2 설정
- 페리티비트 설정(사용, 미사용, 짝수패리티, 홀수패리티 선택)
- 에러검출기능 (페리티에러, 오버런에러, 프레임에러)
- 노이즈필터링(3번 샘플링 동작)  
#### USART 구성
![XCKn](https://cafeptthumb-phinf.pstatic.net/20130101_70/passionvirus_1357003210184DzItk_JPEG/57-1_passionvirus.jpg?type=w740)  
- 클럭발생부 : 보레이트발생기, 외부클럭입력 회로
- 비동기 일반모드   : 내부클럭으로 보레이트발생
- 비동기 2배속모드  : 내부클럭으로 2배의 보레이트발생
- 동기 마스터 모드 
- 동기 슬레이브 모드
- 송신부 : 송신버퍼, 송신 시프트 레지스터, 패리티발생기
- 수신부 : 수신버퍼, 수신 시프트 레지스터, 패리티검사기  
#### USART 인터럽트
- 수신완료 인터럽트
> 데이터가 수신되어 수신시프트레지스터에서 수신버퍼(UDRn) 레지스터로 전달될 때 인터럽트가 걸린다.
> 이 시점에서 사용자는 UDRn을 Read하면 수신데이터를 확인 할 수 있다.
- 송신완료 인터럽트
> 송신 시프트 레지스터에 있는 송신데이터가 모두 전송되어 비어있고 송신버퍼(UDRn) 도 비어있을 때 인터럽트가 발생된다.
- 송신 데이터 준비완료 인터럽트
> 송신 시프트 레지스터로 UDRn값이 전송되어 송신버퍼(UDRn)가 비어 있을 때 인터럽트가 발생한다.
> 이 시점에서 사용자는 UDRn에 새로운 데이터를 Write하면 송신할 수 있게 된다.  
#### 전송데이터 포맷
![전송데이터 포맷](https://cafeptthumb-phinf.pstatic.net/20130101_81/passionvirus_1357003210429sPx5a_JPEG/58-1_passionvirus.jpg?type=w740)  
- ATmega128 의 USART는 전송데이터의 포맷을 여러 형태로 설정할 수 있도록 되어있다.
- Start bit   : 1                         (표준:1 bit)
- Data bit    : 5, 6, 7, 8 or 9       (표준:8 bit)
- Parity bit  : No, Even or Odd  (표준:0 bit)
- Stop bit    : 1 or 2                 (표준:1 bit)  
#### 비동기모드에서의 1비트 샘플링
![샘플링](https://cafeptthumb-phinf.pstatic.net/20130101_202/passionvirus_13570032106854kPt1_JPEG/58-2_passionvirus.jpg?type=w740)  
- 수신데이터의 정확한 검출을 위하여 1비트의 샘플링이 이루어진다.
- 검출을 하기 위해서 1비트당 16배 주파수(2배속모드 : 8배주파수)를 이용한다.
- 16배 주파수중 중앙부(8,9,10)에 3번 샘플링하여 2번 이상인 값으로 처리
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

## AD Convertor
- 연속적인 신호인 아날로그 신호를 부호화된 디지털 신호로 변환하는 일. 아날로그 디지털 변환을 수행하는 기계 장치를 아날로그 디지털 변환기(AD Convertor)라고 하는데, 이 장치는 온도, 압력, 음성, 영상 신호, 전압 등의 실생활에서 연속적으로 측정되는 신호를 컴퓨터에 입력하여 디지털화시키는 장치이다.'
#### A/D 컨버터 특징  
- 8채널/ 내부 아날로그 멀티플렉서 탑재
- 10비트 분해능
- 축차 비교형(변환시간 수십us의 종속형 A/D Convetor)
- 샘플/홀드회로 탑재로 인하여A/D 동작 동안 전압 고정화
- 단극성 아날로그 입력/ 차등입력 선택
- 차등입력에서 10배~200배의 증폭A/D 가능
- 포트F는 아날로그 비교기 기능으로도 사용 가능
- 변환시간(65us ~ 260us - 50kHz ~ 200kHz)  
#### A/D 컨버터 구성
- 범용PORTF의 특수 기능
> ADC0 ~ ADC7 : 8채널10비트A/D 컨버터의 아날로그 입력단자  
- ADC 정확도 성능 향상을 위한 독립 전원 구성
> AVCC : Analog Supply Voltage(VCC의 전압의 ±0.3V 유지 해야함)  
> AGND : Analog Ground (반드시GND와 연결)  
> AREF : Analog Reference Voltage  
- 일반 모드 입력전압의 범위 : 0V ~ Vref
- 차동입력 모드 입력전압의 범위 : -Vref~ Vref
- Vref의 범위는 전원전압VCC를 초과할 수 없다.(Vref=VGND~VCC, 내부기준전압2.56V)  
#### A/D 변환 오차
1. 양자화 오차 : 아날로그 값을 디지털 값으로 변환하면서 생기는 변환의 한계. 분해능이 높은 ADC 필요
2. 오프셋 오차 : 변환 결과가 이상적인 값에서 일정한 양만큼 오차 발생. 변환된 값에 일정한 값을 더하거나 빼서 교정
3. 이득 오차 : 변환 결과가 이상적인 값에서 일정한 비율만큼 오차 발생. 변환된 값에 일정한 값을 곱하거나 나눠서 교정
4. 비선형 오차 : 변환 결과가 교정될 수 없는 상태
5. 차등 비선형 오차 : 변환 결과가 교정될 수 없는 상태  
#### A/D 컨버터 잡음 제거
- A/D Convertor는 노이즈에 매우 민감하여 ATmega128 내부에서도 AVCC,AREF,AGND의 ADC 전원 구성도 따로 하였다.
1. 아날로그 입력선은 최소한으로 짧게 하고 잡음의 영향이 없도록 회로를 구성한다.
2. 아날로그 전원단자 AVCC에 VCC를 인가할 때는LC필터를 거쳐 안정하 시킨다.
3. 아날로그 회로의 모든 접지는 AGND에 접지하여 한 포인트에서만 GND와 접속한다.
4. ADC 동작중에는 병렬I/O 포트의 논리상태를 스위칭 하지 않는다.
5. 잡음에 민감한 아날로그 소자의ADC의 경우에는 Adc Noise Reduction mode를 사용한다.
6. 잡음이 심하여 결과값의 변동이 심하면 디지털 필터를 사용하거나 평균치를 구하여 사용한다.
### 관련 레지스터
1. ADMUX(ADC Multiflexer Selection Register)
> Bit 7,6 - REFS1,REFS0(Reference Selection)
>> 0 0 : AREF 단자에 입력된 전압 사용
>> 0 1 : AVCC 단자에 입력된 전압 사용
>> 1 1 : 내부 2.56V 사용  

> Bit 5 - ADLAR(ADC Left Adjust Result)
>> 1 : 변환 결과값을 ADCH/L에 저장할 때 좌측으로 끝을 맞추어 저장함

> Bit 4,3,2,1,0 - MUXn(Analog Channel and Gain Selection)  
> ![Muxn](https://cafeptthumb-phinf.pstatic.net/20130101_57/passionvirus_1357007171225eDY2d_JPEG/70-3_passionvirus.jpg?type=w740)  

2. ADCSRA(ADC Control and Status Register A)
> Bit 7 - ADEN (ADC Enable)
>> 1 : ADC 활성화

> Bit 6 - ADSC (ADC Start Conversion)
>> 1 : ADC 변환 시작 (단일변환모드일 때 한번만 작동/ 프리런닝모드일 때 변환동작 반복)

> Bit 5 - ADFR (ADC Free Running Mode)
>> 1 : 프리런닝모드
>> 0 : 단일변환모드

> Bit 4 - ADIF (ADC Interrupt Flag)
>> ADC 변환완료 인터럽트가 요청되고 그 상태를 표시하는 비트

> Bit 3 - ADIE (ADC Interrupt Enable)
>> 1 : ADC Interrupt 활성화

> Bit 2,1,0 - ADPS2,1,0 (ADC Prescaler Select)  
> ![ADPS](https://t1.daumcdn.net/cfile/tistory/2761DA3C576CC99314)  

3. ADCH/L (ADC Data Register)  
![ADCH/L](https://cafeptthumb-phinf.pstatic.net/20130101_49/passionvirus_1357007171631mT632_JPEG/72_passionvirus.jpg?type=w740)  
- ADC 변환결과를 저장한다

### A/D Converter의 동작
- 단일변환모드(Single conversion mode)           : ADC 동작을 한번만 수행하게 된다.
- 프리런닝모드(Free running mode)                  : ADC 동작을 반복적으로 수행하게 된다.
1. A/D 컨버터 초기화 설정
- ADC 활성화(ADEN=1)
- ADC 클럭설정(ADPS 2~0)
- ADC 기준전압설정(REF 1~0)
- ADC 입력채널설정(MUX 4~0)
- ADC 동작모드설정(ADFR)
- ADC 변환 완료 인터럽트 활성화(ADIE)
2. A/D 컨버터 스타트
- ADC 시작신호(ADSC)
3. A/D 변환
- ADC 변환 결과  
> ![ADC](https://cafeptthumb-phinf.pstatic.net/20130101_191/passionvirus_1357007171837CdpwH_JPEG/73-1_passionvirus.jpg?type=w740)  
4. A/D 변환 완료
- ADC 변환이 완료되면 변환값을 ADCH/L에 저장
5. A/D 변환 완료 인터럽트 요청
- ADC Conversion Complete Interrupt 요청
6. ADC 상태 플래그 셋
- ADIF = 1
7. ADC 다음 동작 결정
- 단일/연속동작인지 구분하여 다음 동작 수행을 결정한다. (ADFR)

## SPI
- SPI(Serial Peripheral Interface)는 Motorola사에 의하여 개발된 근거리용 직렬통신 규격으로서, MOSI, MISO, SCK, /SS의 4개 통신선을 이용하는 고속 동기식 직렬통신 방식이다. 이는 UART 통신 규격에 비하여 빠른 속도와 멀티 통신이 지원되며, I2C 통신 규격에 비하여 빠르며 간단한 제어가 장점으로 꼽힌다. 그래서 최근 SD메모리나 EEPROM 같은 외부 디바이스들이 SPI통신을 지원하기 시작했다. SPI통신은 장치간에 1:1 통신 중 근거리에서 빠른 속도의 데이터 전송이 요구될 때 사용하는 것을 추천한다.  
#### SPI 특징
• MOSI, MISO, SCK, /SS 의 4선을 사용하는 직렬 동기식 통신  
• 전이중 통신이 가능  
• 항상 Master와 Slave 사이에서 직렬로 데이터를 송수신  
• 클럭은 항상 Master가 발생  
• LSB(최하위비트) or MSB(최상위비트)에서 전송 가능  
• 수 MHz의 통신 가능 및 7가지 전송속도 중 선택 사용  
• 전송완료 인터럽트 발생  
• Wake-Up : 슬립모드 해제기능  
• 직렬통신뿐만 아니라 사용자 프로그램을 다운로드 하는 ISP로서도 이용 가능  
#### SPI 구성
![SPI 구성](https://cafeptthumb-phinf.pstatic.net/20130101_55/passionvirus_1357008785147gJpUS_JPEG/75-1_passionvirus.jpg?type=w740)  
※ SPI는 마스터/슬레이브 모드에 따라 데이터의 입출력이 변경시켜야 하는데 데이터시트에 의하면 유저정의도 가능하나 데이터의 충돌을 피하기 위해서는 위 표의 입출력사항을 지켜줘야 한다.
### 관련 레지스터
1. SPCR (SPI Control Register)  
![SPCR](https://cafeptthumb-phinf.pstatic.net/20130101_276/passionvirus_1357008785583CCdHT_JPEG/77-1_passionvirus.jpg?type=w740)  
- Bit 7 - SPIE (SPI Interrupt Enable)
> 1 : SPI 인터럽트 기능 활성화
- Bit 6 - SPE (SPI Enable)
> 1 : SPI 동작 활성화
- Bit 5 - DORD (Data Order)
> 1 : LSB가 먼저 전송  
> 0 : MSB가 먼저 전송
- Bit 4 - MSTR (Master/Slave Select)
> 1 : 마스터 모드  
> 0 : 슬레이브 모드
- Bit 3 - CPOL (Clock Polarity)
> 1 : IDLE 상태일 때 SCK = High  
> 0 : IDLE 상태일 때 SCK = Low  
> ![CPOL](https://cafeptthumb-phinf.pstatic.net/20130101_69/passionvirus_1357008785786QzzRG_JPEG/77-3_passionvirus.jpg?type=w740)  
- Bit 2 - CPHA (Clock Phase)
> 데이터 샘플링 동작이 수행되는 SCK의 위상 결정  
> ![CPHA](https://cafeptthumb-phinf.pstatic.net/20130101_170/passionvirus_13570087859716wOrg_JPEG/77-4_passionvirus.jpg?type=w740)  
- Bit 1,0 - SPR1,0 (SPI Clock Rate Select)  
> ![SPR](https://cafeptthumb-phinf.pstatic.net/20130101_291/passionvirus_1357008786600BloAl_JPEG/79-1_passionvirus.jpg?type=w740)  
> 이 두비트는 마스터로 선택된 장치의 SCK의 속도를 조정한다. SPR1과 SPR0는 슬레이브에게는 영향을 주지 않는다.
2. SPSR(SPI Status Register)  
![SPSR](https://cafeptthumb-phinf.pstatic.net/20130101_65/passionvirus_1357008786832DBkpJ_JPEG/79-2_passionvirus.jpg?type=w740)  
- Bit 7 - SPIF : SPI Interrupt Flag
> 1 : 시리얼 전송이 완료되면 SET
- Bit 6 - WCOL : Write COLision Flag
> 1 : SPI데이터 레지스터가 전송 중에 쓰여질 때(written) Set 된다.
- Bit 0 - SPI2X : 2배속 SPI 비트
> 1 : 마스터 모드일 때 SPI의 속도( SCK 주파수)는 2배가 된다.
3. SPDR(SPI Data Register)  
![SPDR](https://cafeptthumb-phinf.pstatic.net/20130101_216/passionvirus_13570087870091Q42n_JPEG/79-3_passionvirus.jpg?type=w740)  
- SPDR은 SPI가 송신할 데이터를 저장하거나 수신한 데이터를 저장하게 된다. 사용자는 이 레지스터에 데이터를 쓰는 것으로서 SPI 송신을 수행하게 되며 읽는 것으로서 SPI 수신이 이루어지게 된다.  
#### SPI 동작 순서
1. 마스터 모드
> /SS, SCK, MOSI 단자를 출력으로 설정, MISO 단자를 입력으로 설정 
> /SS 신호를 LOW로 출력하여 슬레이브 선택  
> SPDR 레지스터에 전송할 바이트를 라이트 하여 송신 시작  
> SCK클럭 발생하고 하드웨어적으로 데이터를 8Bit만큼 시프트하여 슬레이브로 전송  
> 1바이트 전송이 끝나면 클럭이 정지되고 SPIF 비트가 1되면서 SPI 전송완료 인터럽트 요청  
> 이 후 SPDR 레지스터에 바이트를 라이트 하면 그 다음의 전송이 시작  
> 데이터 패킷을 종료하려면 /SS 단자에 HIGH 신호 출력  
2. 슬레이브 모드  
> /SS, SCK, MOSI 단자를 입력으로 설정, MISO 단자를 출력으로 설정  
> SPI가 슬레이브로 동작 시 /SS 단자가 자동으로 입력핀으로 동작  
> /SS 신호가 LOW 상태로 입력된 경우에 SPI는 슬레이브로 동작이 유효하게 된다.  
> 만약 /SS 신호가 HIGH 상태로 입력되면 모든 SPI 신호가 3스테이트 또는 입력 상태로 변함  
> /SS 신호가 LOW 상태로 입력되고 외부에서 입력된 SCK 신호에 의하여 데이터 레지스터의 1바이트가 전송되고 나면 SPIF 비트가 1로 되면서 SPI전송완료 인터럽트가 요청.  
> SPI 전송완료 인터럽트를 체크하여 수신데이터를 보낸다.

## I2C  
![I2C](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbfk5A2%2FbtqxmvIKcDi%2FLqEGKLB9cIiQkP1KCeu4Qk%2Fimg.png)  
- I2C(Inter-Intergrated Circuit)는 TWI(Two-Wire Serial Interface)라고도 부른다. 클럭과 데이터로 구성된 두 개의 버스 선(SDA, SCL)으로 이루어져 있으며, 7비트로 128개의 서로 다른 디바이스를 제어할 수 있다. 각 버스선 마다 풀업 저항이 연결되어 있으며, 마스터와 슬레이브 동작을 동시에 지원한다. 데이터 선이 하나 밖에 없으므로 반이중(half-duplex) 통신을 할 수 밖에 없고 SPI와 마찬가지로 마스터-슬레이브 구조를 따른다.
![TWI 모듈](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdctN3C%2FbtqxomYBHMS%2FOx7lLEPDi4CCKigOGEZ8Lk%2Fimg.png)  
### 레지스터  
1. TWBR (TWI Bit Rate Register) : TWI 전송 속도 (SCL 클럭 주파수) 설정
![TWBR](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbHLLSI%2FbtqxnnKelYo%2FznV9B9eMkdRuksbMUQk9DK%2Fimg.png)  
![SCL frequency](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcCxK0b%2Fbtqxpe6RHRS%2FEqlkWRkiUtS7Pqw7KzBa1k%2Fimg.png)  
2. TWCR (TWI Control Register) : TWI 동작 제어  
![TWCR](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FxS4wQ%2FbtqxoPTMRi0%2Fc1kefzLP5qkaJCNfGF2pE1%2Fimg.png)  
- Bit 7 - TWINT (TWI INTerrupt flag) : 
- Bit 6 - TWEA (TWI Enable Acknowledge) :
- Bit 5 - TWSTA (TWI Start) :
- Bit 4 - TWSTO (TWI STOP) :
- Bit 3 - TWWC (TWI Write Collision flag) :
- Bit 2 - TWEN (TWI ENable) : TWI 활성화
- Bit 0 - TWIE (TWI Interrupt Enable) : TWI 인터럽트 활성화
3. TWSR (TWI Status Register)  
![TWSR](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F8otY3%2FbtqxoPz1Asj%2FmQAZtsHN35YGIUkEeBkR0K%2Fimg.png)  
- Bit 7 ~ 3 - TWI의 로직, 상태를 나타냄  
- Bit 1,0 - TWPS1,0 (TWI PreScaler)  
![TWPR](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fmlz7D%2FbtqxonKwAYr%2FsgT7x1ARTNEfAreO2vEak0%2Fimg.png)  
4. TWDR (TWI Data Register) : 송신할 때는 전송할 데이터 저장, 수신일 때는 이전 데이터 저장  
![TWDR](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FFsE2q%2FbtqxpdAHAMa%2FQ9pcs4f38kCornhl8Ux9HK%2Fimg.png)  
5. TWAR (TWI Address Register)  
![TWAR](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FMBbek%2FbtqxqWSx4E5%2FXRKV0rotnMWJuJZDy8d1C0%2Fimg.png)  
- Bit 7 ~ 1 - TWA6 ~ 0 (TWI Address) : 수신할 7비트의 slave 주소  
- Bit 0 - TWGCE (TWI General Call Enable) : 1일 때 general call을 인식. 마스터에서 START신호를 발생하고 TWDR에 0x00을 넣으면 general을  의미하며, TWGCE가 1인 slave들이 응답하게 된다.  
#### TWI 통신의 동작  
![TWI 동작](https://t1.daumcdn.net/cfile/tistory/21052B485713168918)  

