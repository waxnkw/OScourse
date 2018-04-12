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

## 二 寻址方式
1. 存储字的方式：0x12345678-地址由高到低>12,34,56,78.以低地址代表数字的地址，也就是78位置

2. 存储器分段:
>> 20位地址，寻址1M空间
>> XXXXx: 段地址:XXXX 偏移:x 物理地址=段地址<<2+偏移
>> 取指令 段：CS 偏移IP
>> 堆栈操作 段:SS 偏移SP
![段寄存器引用规定](https://github.com/waxnkw/OScourse/raw/master/resource/img/asm/asm1.PNG)

## 三 8086寻址方式
1. 立即寻址: mov ax, 1234H 给寄存器or储存单元赋初值
2. 寄存器寻址: mov si, ax
3. 直接寻址: DS=5000H <code>mov ax, [1234]//不明确指出DS</code>
![段寄存器引用规定](https://github.com/waxnkw/OScourse/raw/master/resource/img/asm/asm2.PNG)

4. 寄存器间接寻址: mov ax, [si]。 
>> 非默认的 mov ax,CS:[bx]
>> 默认的段地址如下:
![段寄存器引用规定](https://github.com/waxnkw/OScourse/raw/master/resource/img/asm/asm1.PNG) 

>> 表格处理 <code>mov [bp],cx</code>

5. 寄存器相对寻址: EA=bx/bp/si/di + 8/16位移量
>> <code>mov ax, [di+1223h]//默认为ds</code> 代表ds+di+1223h
>> 同样可以用于表格处理
![段寄存器引用规定](https://github.com/waxnkw/OScourse/raw/master/resource/img/asm/asm3.PNG)

6. 基址加变址的方式: EA = BX/BP + SI/DI
>> 有bp:ss  有bx:ds
>> <code>mov ax,[bx+di]</code>
>> 可以用于数组处理
![段寄存器引用规定](https://github.com/waxnkw/OScourse/raw/master/resource/img/asm/asm4.PNG)

7. 相对地址加变址
>> <code>mov ax, [bx,di,1234h]</code>

## 四 指令的使用
可以访问 [asm指令](http://www.felixcloutier.com/x86/ "asm指令") 进行查询，里面包括了各种指令的兼容性问题，以及指令的具体使用的介绍，十分方便。

## 五 函数调用方式
1. call, leave, ret, enter:
>> call: <code>push eip</code> 进行当前执行指令的改变
 leave: <code>mov esp,ebp; pop ebp</code> 进行函数栈帧的恢复，即回到调用者函数栈
 enter: <code>push ebp; mov ebp,esp</code>  进行函数栈帧的改变,即进入被调用函数函数栈
 ret : <code>pop eip</code>  回到调用者指令部分,继续执行
 写的时候: <code>call f</code>  <code>ret</code>
 or call enter leave ret

2. linux64位系统下syscall与int 80h不同,不能混用。没有查清楚,不多加赘述。



## 六 个人代码书写范式
    会从函数书写方式，调用协议;注释风格;条件语句设计;循环语句设计进行介绍。
    部分模仿了c语言格式,但是在栈帧的改变方面,返回值设计方面都有不同
    背景: 64位linux下,使用64位的寄存器
### 1 个人函数调用
1. 寄存器使用:    
>>传参寄存器: rdi, rsi, rdx, r8, r9, r10, r11, r12, r13, r14, r15

  函数内使用寄存器: r8及以后,未用来传参的寄存器。比如使用 rdi, rsi传参,则在函数内部可使用r8到r15进行计算。有时也会有使用其他寄存器的特例。
 
  返回值寄存器:rax rbx rcx。 为简化代码,允许三个以内的返回值 

2. 寄存器保存
>>caller-save(调用者保存): params即用来进行参数传递的寄存器

>>callee-save(被调用者保存): 内部计算储存局部变量的寄存器

3. 具体实现过程
>>个人倾向于通过规范化的注释来完成函数与调用者的协议声明。  
首先是函数书写:   
![asm5](https://github.com/waxnkw/OScourse/raw/master/resource/img/asm/asm5.PNG)    

>>swap two mem  本函数的作用,即交换两个内存区域   
rely:  需要使用的全局变量。目的是以后进行函数复用的时候,提醒对于函数外部全局变量的依赖。比如在本例中需要使用buf1来做swap的中转站   
caller-save: 需要由调用者保存的寄存器   
param: 参数传递的寄存器,以及其具体的含义  
callee-save: 被调用者内部使用的寄存器。  

![asm6](https://github.com/waxnkw/OScourse/raw/master/resource/img/asm/asm6.PNG)
>>函数内部书写: 如上图所示,在函数开始部分进行需要使用的寄存器的保存,在函数结束后,按照相反的顺序pop还原

![asm7](https://github.com/waxnkw/OScourse/raw/master/resource/img/asm/asm7.PNG)
>>调用方式: 如上图,调用提及的print_s函数。
首先按照协议保存caller-save registers <code>push rdi</code> <code>push rsi</code>         
第二步进行调用函数参数的填充       
第三步进行函数的调用 <code>call print_s</code>      
第四步进行保存参数的恢复 <code>pop rsi</code>  <code>pop rdi</code>     
最后如果有返回值,则进行返回值处理   
补充说明: 建议每次都采取这种傻瓜式push pop,不要为了节省代码而少保存几个caller-save寄存器。
比如当前rdi填充callee使用的参数,且没有进行传递前保存,结果在callee中修改了其中的值。而rdi中的值在下文中仍需要使用.
这时callee修改了rdi的值,就翻车了。   
作者之前,因为rdx做计数器,没保存,结果被callee改了,de了半天bug   
//TODO
