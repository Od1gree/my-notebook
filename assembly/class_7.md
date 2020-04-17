```x86asm
;以下二者等价,均为[x的地址]+4,然后将此地址的值送到eax
mov eax,x+4 ;都是直接寻址
mov eax,[x+4] ;直接寻址
```

```x86asm
mov eax,12345678H ;立即数寻址
MOV EAX,ECX ;寄存器寻址
MOV EAX,[12345678H] ;DS:[]直接寻址
MOV EAX,[EBX] ;寄存器间接
MOV EAX,[EBX+12345678H] ;寄存器相对
;顺序为2,1,4,3,5由快到慢
```


