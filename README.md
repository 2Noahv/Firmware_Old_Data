#include <stdio.h>

int main(void)
{
	int yyy , mmm,ddd;
	scanf("%d.%d.%d", &yyy, &mmm,&ddd);
	printf("%04d.%02d.%02d", yyy,mmm,ddd);  // 04d = 4자리 출력, 02d = 2자리 출력
                                          // ex 21.2.28 입력시 => 0021.02.28 
}
