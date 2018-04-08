# asm语法

## 一 寄存器

1. 数据寄存器
>>  1. AX：AL AH，累加器
>>  2. BX base寄存器
>>  3. CX count寄存器
>>  4. DX data寄存器

2. 运算结果寄存器
>>  1. ZF零标志位
>>  2. CF进位标志位：指示是否产生进位or错位，指示无符号数加减是否溢出
>>  3. SF符号位：SF与运算结果的最高位相同。指示有符号运算的最高位
>>  4. OF溢出位，指示有符号数加减是否溢出
>>  5. PF奇偶标志位，反映计算结果中1的个数
>>  6. AF辅助进位标志， 在进行字节操作时，低字节向高字节进位，则置1。

## 寻址方式
1. 存储字的方式：0x12345678-地址由高到低>12,34,56,78.以低地址代表数字的地址，也就是78位置

2. 存储器分段:
>> 20位地址，寻址1M空间
>> XXXXx: 段地址:XXXX 偏移:x 物理地址=段地址<<2+偏移
>> 取指令 段：CS 偏移IP
>> 堆栈操作 段:SS 偏移SP
![段寄存器引用规定](../resourse/img/asm/asm1.PNG)

## 8086寻址方式
1. 立即寻址: mov ax, 1234H 给寄存器or储存单元赋初值
2. 寄存器寻址: mov si, ax
3. 直接寻址: DS=5000H <code>mov ax, [1234]//不明确指出DS</code>
![段寄存器引用规定](../resourse/img/asm/asm2.PNG)

4. 寄存器间接寻址: mov ax, [si]。 
>> 非默认的 mov ax,CS:[bx]
>> 默认的段地址如下:
![段寄存器引用规定](../resourse/img/asm/asm1.PNG) 

>> 表格处理 <code>mov [bp],cx</code>

5. 寄存器相对寻址: EA=bx/bp/si/di + 8/16位移量
>> <code>mov ax, [di+1223h]//默认为ds</code> 代表ds+di+1223h
>> 同样可以用于表格处理
![段寄存器引用规定](../resourse/img/asm/asm3.PNG)

6. 基址加变址的方式: EA = BX/BP + SI/DI
>> 有bp:ss  有bx:ds
>> <code>mov ax,[bx+di]</code>
>> 可以用于数组处理
![段寄存器引用规定](../resourse/img/asm/asm4.PNG)

7. 相对地址加变址
>> <code>mov ax, [bx,di,1234h]</code>

//TODO