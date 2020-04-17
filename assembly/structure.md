```x86asm
MOV EAX,EDI
SHL EAX,2
ADD EAX,EBX

LEA EAX,[EBX+EDI*4]

MOV EAX,ADDR[EBX+EDI*4]

TEST AL,00111000B;运算方式为AND,但不修改AL值.
JNZ L

ADC BYTE PTR [EBX+EAX-1],0
```

|传输|算术|格式|

###位操作
* 逻辑运算:
>AND,OF,NOT,XOR,TEST
* 移位运算
>逻辑移位SHL,SHR;(不考虑正负号,与算术移位操作完全相同,指令针对是否有符号而言,不同)
>算术移位SAL,SAR;(考虑正负号)
>循环移位ROL,ROR;
>带进位循环移位(带CF位)RCL,RCR;

所有C语言函数的返回值都在累加器中(EAX,EDX)

|串传送|MOVSX|源|目的|前缀|
|:-|
|X|B,W,D,Q|ESI|EDI|REP|
|串比较|CMPSX|ESI|EDI|REPZ|
|串扫描|SCASX|累加器|EDI|REPNZ|
|取一元素|LODSX|ESI|累加器|无|
|存一元素|STOSX|累加器|EDI|REP|

###串操作的四大准备
* ECX:元素个数
* DF:元素处理方向(正0负1)
* 源:ESI
* 目的:EDI

串指令执行:
* MOV [EDI],[ESI]
* ESI指向下一元素
* EDI指向下一元素

###串存储(写元素)
* SRC:ACC
* DST:ESI

以下为栗子

```x86asm
.DATA
    V   DWORD   COUNT DUP(0)
.CODE
    MAIN PROC
        MOV ECX,COUNT
        CLD
        MOV EAX,0FFFFFFFH
        LEA EDI,V
        REP STOSD
```

###串读取(读元素)
* SRC:ESI
* DST:ACC(累加器)

##子程序
* 子程序:独立功能的模块,C中为函数,pascal为procedure或函数.
* 当多次调用子程序时,比其他方法减少代码长度,模块化,易读性好.
* 在主程序里用CALL P调用子程序P,在子程序里用RET返回主程序(实际指令:POP EIP).
* 调用和返回是通过堆栈来实现的:把CALL的<font color=red>下一条指令</font>的地址压栈.
* 子程序的名字P本质就是地址,可以段内调用,也可以跨段调用.


* 子程序可以有参数
> 参数传递可以采用寄存器,全局变量,堆栈,也可用外设或IO端口
* 子程序可以返回值
> 返回值用寄存器或全局变量,不能用堆栈.
* 子程序内都可以有局部变量
> 局部变量要在堆栈段中申请,不在代码段.
>
* 进入子程序里执行程序会修改寄存器的值,所以应该把被修改的寄存器保护(压栈),子程序返回前应把他们返回原状.
* 子程序中需要对参数或局部变量访问,访问时采用[EBP+-...]形式
* 子程序可以递归,但会占用大量堆栈.要保证堆栈空间够用,否则堆栈溢出.

* 子程序调用回到主程序应该把占用的空间释放,实现堆栈平衡
>1. win的标准调用STDCALL函数,用RET n在子程序实现堆栈平衡
>2. C语言函数的C调用,必须在主程序中通过ADD ESP,N*4来堆栈平衡.
>3. C通过这种方式实现可变个数的参数,即VARARG:编译器识别参数个数,增加相应的堆栈平衡指令.

####子程序声明
* 纯一模式--标号模式
> F: MOV EAX,1
>.....
>RET
* PROC/ENDP模式
> 如MAIN函数
* PROTO/INVOKE模式
>如printf等先声明后使用

```x86asm
.DATA
    V       DWORD   COUNT DUP(?)
    Le      DWORD
    S       DWORD   0
.CODE
    SUM     PROC
            PUSH    EBP
            PUSH    ESI;保护现场
            MOV     EAX,0
            MOV     ESI,0
        L:  ADD     EAX,[EBX+ESI*4}
            INC     ESI
            LOOP    L
            ....
            POP     ESI;恢复现场
            RET
    SUM     ENDP

    MAIN    PROC
            ....
            MOV     ECX,COUNT;约定为数组元素个数
            LEA     EBX,V;约定为v首地址
            CALL    SUM

            INVOKE  PRINTF(),EAX;eax约定为返回值
```


