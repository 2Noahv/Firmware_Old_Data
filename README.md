#define F_CPU 16000000UL
#include <avr/io.h>
#include <avr/interrupt.h>
#include <stdio.h>

#include "lcd.h"


#define MAX_LCD_STRING 0x40

void ADC_init(void)
{
	ADMUX = 1<<REFS0;
	// AVCC가 선택된다. 이 때 AREF 핀에 콘덴서를 연결한다.
	ADCSRA |= 1<<ADEN | 1<<ADIE | 7;
	// ADC 활성화
	// ADC 인터럽트 활성화
	// 프리스케일러 분주비 128 설정
	// 외부핀 AREF 사용, ADC 채널 (0,0,0,0,0) : 단일 신호 ADC0 핀
}

void	LCD_str_write(unsigned int row, unsigned int col, char *str)
{
	int	i;
	
	set_cursor(row, col);
	for(i=0; (i+col < MAX_LCD_STRING) && (str[i] != '\0'); i++)
	LCD_data_write(str[i]);
}

int main(void)
{
	char lcd_string[2][MAX_LCD_STRING];
	
	ADC_init();
	LCD_init();
	
	sprintf(lcd_string[0], "Temp= ");
	sprintf(lcd_string[1], "ADC result= ");
	
	LCD_str_write(0, 0, lcd_string[0]);
	LCD_str_write(1, 0, lcd_string[1]);

	ADCSRA |= 1<<ADSC;	
	// A/D 변환 곧바로 시작
	
	while(1){
		float volt;
		volt = ADC * 5 / 1023.0*100;
		sprintf(lcd_string[0], "%4.2f[`C]", volt);
		sprintf(lcd_string[1], "%-4d ", ADC);
		LCD_str_write(0, 6, lcd_string[0]);
		LCD_str_write(1, 12, lcd_string[1]);
		ADCSRA |= 1<<ADSC;	
	}
	return 0;
}

/// LCD 
#define	RS	PB0	// LCD 문자디스플레이에 연결된 포트D 의 핀번호
#define	RW	PB1
#define	E	PB2

void	gen_E_strobe(void)
{	volatile	int	i;

	PORTB |= 1<<E;		// E 신호를 High로
	for(i=0; i<10; i++);		// E 스트로브 신호를 일정기간 High로 유지
	PORTB &= ~(1<<E);		// E 신호를 Low로
}

void	wait_BusyFlag(void) // 8핀 Data 핀의 사용유무를 체크한 후에 새로운 데이터 전송을 위해 사용됨.
{	volatile int 		i;
	unsigned char		bf;

	DDRD = 0x0;			// 포트E를 입력핀으로 설정
	PORTB = (PORTB & ~(1<<RS)) | 1<<RW; // RS <- Low, RW <- High
	do{
		PORTB |= 1<<E;		// E 신호를 High로
		for(i=0; i<10; i++);	// E 스트로브 신호를 일정기간 High로 유지
		bf = PIND & 1<<PD7;	// busy flag 읽어 냄
		PORTB &= ~(1<<E);	// E 신호를 Low로
	}while( bf );			// bf = 1 이면  do ~ while 문 반복으로 buffer 시간 가진다.
}

void	LCD_command(unsigned char data)
{	wait_BusyFlag();		// busy flag가 0될 때까지 대기
	DDRD = 0xFF;			// 포트E를 출력핀으로 설정
	PORTD = data;			// data 출력
	PORTB &= ~(1<<RS | 1<<RW);	// RS <- 0, RW <-0
	gen_E_strobe();		// E 스트로브 신호 만들기
}

void	LCD_data_write(unsigned char data) // 문자출력 함수
{	wait_BusyFlag();
	DDRD = 0xFF;
	PORTD = data;
	PORTB = (PORTB | 1<<RS) & ~(1<<RW); // RS <- 1, RW <-0
	gen_E_strobe();
}

void	LCD_init(void)
{
	DDRB |= 1<<RS | 1<<RW | 1<<E;	// RS, RW, E 핀을 출력핀으로 설정
	
	PORTB &= ~(1<<RS | 1<<E | 1<<RW); 	// 초기에 RS, E, RW <- 0

	LCD_command(0x3C);	// 8비트 인터페이스, 두줄표시, 문자 5x10도트
	LCD_command(0x02);	// 커서 초기위치 설정
	LCD_command(0x01);	// 화면 클리어
	LCD_command(0x06);	// 커서를 오른쪽으로 이동하면서 문자 입력
	LCD_command(0x0C);	// 디스플레이, 커서
}

void set_cursor(unsigned int col, unsigned int row)
{

	switch(col){
		case 0 : col=0x80; break;
		case 1 : col=0xc0; break;
	}
	col = col+row;
	LCD_command(col);
}
