# Atmega128

## 타이머/카운터  
### 타이머/카운터0번  
- 8비트 계수기 (0~255 계수 가능, TCNT0에 저장)  
- overflow interrupt : TCNT0가 255에서 1이 증가하면 0이 되고 이 때 Overflow Interrupt 발생  
- compare match interrupt : TCNT0가 OCR0값을 비교하여 같으면 Interrupt 발생

#### 관련 레지스터  
1. TIMSK(Timer/Counter Interrupt Mask Register) : 타이머/카운터 0~2에서 발생하는 인터럽트를 개별적으로 허용하는 기능을 수행하는 레지스터  
![TIMSK](https://t1.daumcdn.net/cfile/tistory/999C8C3359C377F533)
> BIT 1 - OCIE0 (Output Compare Match Interrupt Enable) : T/C0 비교일치 인터럽트 활성화  
  BIT 0 - TOIE0 (Timer/Counter Overflow Interrupt Enable) :  T/C0 오버플로우 인터럽트 활성화  

2. TIFR(Timer/Counter Interrupt Flag Register)  
![TIFR](https://t1.daumcdn.net/cfile/tistory/9957FC3359C37A921B)
> BIT 1 - OCF0 (Output Compare Match Flag) : TCNT0 = OCR0 경우 1로 set되고 인터럽트 요청  
  BIT 0 - TOV0 (Timer/Counter Overflow Flag) : 오버플로우 발생시 1로 set되고 인터럽트 요청  

3. TCCR0(Timer/Counter Control Register 0) : 동작모드 / 분주비 설정  
![TCCR0](https://t1.daumcdn.net/cfile/tistory/998BED3359C374FC1A)  
> BIT 7 - FOC0(Force Output Compare) : 강제로 OSC0 단자에 신호 출력 (인터럽트 X)  
  BIT 3,6 - WGM01,WGM00 (Waveform Generation Mode) : 파형 출력 모드  
  > Normal mode : Overflow Interrupt에 사용  
    CTC mode(Compare Timer on Compare Match mode) : Compare Match Interrupt에 사용  
  ![WGM](https://t1.daumcdn.net/cfile/tistory/990BE53359C3773326)  
  BIT 5,4 - COM01,COM00 (Compare Match Output Mode) : 비교매치 출력 모드  
  ![phase correct PWM](https://t1.daumcdn.net/cfile/tistory/992A473359CF36AD17)  
  ![Fast PWM](https://t1.daumcdn.net/cfile/tistory/99BEFA3359CC9DD724)  
  BIT 2,1,0 - CS02,CS01,CS00 분주비 설정 비트  
> <img src="https://t1.daumcdn.net/cfile/tistory/9923DE3359C366A00F" width="500" height="200">  
4. TCNT0(Timer/Counter Register 0) : T/C0의 8비트 카운터 값을 저장하고 있는 레지스터  
5. OCR0(Timer/Counter Output Compare Register 0) : TCNT0와 비교하여 OC0 단자에 출력신호를 발생하기 위한 8비트 값을 저장하고 있는 레지스터  
6. ASSR(Asynchronous Status Register) : T/C0가 외부클럭에 의한 비동기 모드로 동작하는 경우에 관련  
7. SFIOR(Special Function I/O Register)  
  
