# Atmega128

## 타이머/카운터  
- overflow interrupt : TCNTn값이 Max에서 1이 증가하면 0이 되고 이 때 Overflow Interrupt 발생  
- compare match interrupt : TCNTn가 OCRn값을 비교하여 같으면 Interrupt 발생  
### 타이머/카운터 0,2번  
- 8비트 계수기 (0~255 계수 가능, TCNTn에 저장)  
- 1개의 PWM출력(OCn)을 가진다.
#### 관련 레지스터  
1. TIMSK(Timer/Counter Interrupt Mask Register) : 타이머/카운터 0~2에서 발생하는 인터럽트를 개별적으로 허용하는 기능을 수행하는 레지스터  
![TIMSK](https://t1.daumcdn.net/cfile/tistory/999C8C3359C377F533)
> BIT 1 - OCIE0 (Output Compare Match Interrupt Enable) : T/C0 비교일치 인터럽트 활성화  
  BIT 0 - TOIE0 (Timer/Counter Overflow Interrupt Enable) :  T/C0 오버플로우 인터럽트 활성화  
2. TIFR(Timer/Counter Interrupt Flag Register)  
![TIFR](https://t1.daumcdn.net/cfile/tistory/9957FC3359C37A921B)
> BIT 1 - OCF0 (Output Compare Match Flag) : TCNT0 = OCR0 경우 1로 set되고 인터럽트 요청  
  BIT 0 - TOV0 (Timer/Counter Overflow Flag) : 오버플로우 발생시 1로 set되고 인터럽트 요청  
3. TCCRn(Timer/Counter Control Register) : 동작모드 / 분주비 설정  
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
4. TCNTn(Timer/Counter Register) : T/C0의 8비트 카운터 값을 저장하고 있는 레지스터  
5. OCRn(Timer/Counter Output Compare Register) : TCNT0와 비교하여 OC0 단자에 출력신호를 발생하기 위한 8비트 값을 저장하고 있는 레지스터  
6. ASSR(Asynchronous Status Register) : T/C0가 외부클럭에 의한 비동기 모드로 동작하는 경우에 관련  
7. SFIOR(Special Function I/O Register)  
  
### 타이머/카운터 1,3번
- 16비트 계수기 (0 ~ 65535 계수 가능,TCNTn에 저장)  
- 3개의 PWM 출력과 1개의 캡쳐 기능을 가진다.
#### 관련 레지스터  
1. TCCRnA : 채널 A,B,C의 비교 출력 모드/ 파형 발생 모드 설정  
![TCCRnA](https://mblogthumb-phinf.pstatic.net/20160105_21/gwangryr_1451925127720LvTo1_PNG/1.PNG?type=w2)  
2. TCCRnB : 입력 캡쳐 설정/ 파형 발생 모드 설정/ 분주비 설정  
![TCCRnA](https://mblogthumb-phinf.pstatic.net/20160105_166/gwangryr_1451925870955bSqI7_PNG/2.PNG?type=w2)  
3. TCCRnC : 채널 A,B,C의 강제 출력 비교 설정  
