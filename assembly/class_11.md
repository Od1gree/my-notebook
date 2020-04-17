```x86asm
INCLUDE  ASM32.INC

.DATA												; 数据段定义，全局变量在这儿
X	QWORD	123456789ABCDEF0H
		Y	QWORD	0FEDCBA9876543210H
		Z	QWORD	?
.CODE												; 代码段开始
	MAIN	PROC									; 函数 - 子程序开始
			MOV		EAX,DWORD PTR X ;强制转换为32位
			MOV		EDX,DWORD PTR X+4
			ADD		EAX,DWORD PTR Y
			ADC		EDX,DWORD PTR Y+4
			MOV		DWORD PTR Z,EAX
			MOV		DWORD PTR Z+4,EDX

			INVOKE	printf, chr$("Z=X+Y 为：%llX",0DH,0AH),Z
			INVOKE	getchar

			INVOKE	 ExitProcess,0
	MAIN	ENDP											; 函数 - 子程序结束

			END       MAIN									; END 指示所有程序到此结束，第一条可执行语句位置为main
```

#### 从键盘输入一串数据到数组V,对每一个元素求绝对值,打印每一个元素.(假设元素个数为10个),代码如下:

```x86asm
INCLUDE  ASM32.INC

COUNT=10

.DATA												; 数据段定义，全局变量在这儿
		V	DWORD  COUNT DUP(?)
.CODE												; 代码段开始
	MAIN	PROC									; 函数 - 子程序开始
			INVOKE	printf, chr$("请输入数组元素：")
			MOV		ECX,COUNT
			MOV		ESI,0
	L1:		PUSH	ECX
			INVOKE	scanf,chr$("%d"),ADDR V[ESI*4]		; 获得数组元素的地址
			POP		ECX
			INC		ESI
			LOOP	L1

			INVOKE	  getchar

			MOV		ECX, COUNT
			MOV		ESI, 0
	L2:		TEST	V[ESI*4],80000000H			;判断是否为负数，最高位为1
			JZ		LL
			NEG		V[ESI*4]
	LL:		INC		ESI
			LOOP	L2

			MOV		ECX, COUNT
			MOV		ESI, 0
	L3:		PUSH	ECX
			INVOKE	printf,chr$("%8d"),V[ESI*4]
			POP		ECX
			INC		ESI
			LOOP	L3

			INVOKE	  getchar

			INVOKE	  ExitProcess,0
	MAIN	ENDP											; 函数 - 子程序结束

			END       MAIN									; END 指示所有程序到此结束，第一条可执行语句位置为main


```


