##决策伪指令

* 像C语言编写程序的各类结构(带".")
* 但本质上会转换为cmp,jz等汇编语言指令
* 编程时可以采取决策伪指令
* 决策伪指令没有直接写代码高效
* 容易出现编译错误
* 决策伪指令编写仍然要遵循汇编语言的操作数寻址方式,这是编译错误的典型
* 它会导致程序执行错误(加NOP)

##宏

> 宏结构:宏汇编,重复汇编,条件汇编.

* 定义时书写

> 宏:具有宏名的一段汇编语句序列

* 可以无参数,一个参数,多个参数
* 参数可以是常数,变量,存储单元,指令(操作码)或他们的一部分,也可以是表达式
* 宏定义体可以是任何合法的汇编语句,既可以是硬指令序列,也可以是伪指令序列.

```x86asm
宏名 macro [形参表]
    宏定义体
    endm
    ;;注释时要用两个注释符号,称为宏注释符
mainbegin   MACRO  ;;无参数
            mov ax,1
            mov bx,ax
            endm

mainend     MACRO retnum ;;有参数
            mov al,retum
            mov ah,4ch
            int 21h
            ENDM
```

* 调用时书写

> 宏指令:这段汇编语句序列的缩写

```x86asm
宏名 [实参表]
    start: mainbegin
            mainend 0 ;宏调用
            end start
```

* 宏汇编时实现

> 宏展开(宏替换):宏指令处用这段宏代替的过程
> 宏展开的具体过程是：当汇编程序扫描源程序遇到已有定义的宏调用时，即用相应的宏定义体取代源程序的宏指令，同时用位置匹配的实参对形参进行取代

* 宏有参数也有返回值
* 宏调用的实质是在汇编过程中进行宏展开.
*

```x86asm
SHLEXT MAXRO SHLOPRAND,SHLNUM
        PUSH    CX
        MOV     XL,SHLNUM
        SHL     SHLOPRAND,CL
        POP     CX
        ENDM

SHLEXT AX,6


shift	macro soprand,snum,sopcode
	push cx
	mov cl,snum
	s&sopcode& soprand,cl ;;替换操作符
	pop cx
	endm
;调用方式如下
shift   eax,2,ar

;宏定义
dstring	macro string
	db ’&string&’,0dh,0ah,0
	endm
;宏调用
dstring	< This is a example. > ;尖括号括起来,指明是一个参数
dstring	< 0 !< Number !< 10 > ;感叹号是转义注释符
;宏展开
 1	db ’This is a example.’, 0dh,0ah, 0
 1	db ’0 < Number < 10’, 0dh,0ah, 0

```

###宏有关的伪指令

####局部标号伪指令

> LOCAL 局部标识符列表

####宏定义删除伪指令

> PURGE 宏名表

* 先删再用或先用再删

####宏定义退出伪指令

> EXITM

###重复汇编


###条件汇编

* if前面没有"."

