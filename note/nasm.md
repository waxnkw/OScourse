# nasm笔记

## linux下编译
hello.asm  

<code>nasm -f elf64 hello.asm -g -F stabs</code>  
-f elf64代表以elf64位格式来编译文件
-F stabs 用来生成符号表，以便于使用gdb debug

<code>gcc -o hello hello.o -g</code>
使用gcc编译".o"文件为可执行文件

<code>./hello</code>
执行程序  

<code>gdb hello</code>
进入debug模式  

## gdb debug
<code>list  //可简写为l</code> : 列出未执行代码

<code>break 16</code>  在16行设置断点

<code>break main</code>  在main函数处设置断点

<code>run // 可简写为r</code>  运行程序到断点

<code>info break //可简写为i break</code>  查看当前设置的所有断点信息
![图片丢失](https://github.com/waxnkw/OScourse/raw/master/resource/img/breakInfo.PNG)

<code>bt</code>  显示当前栈数据

<code>info registers</code>  显示当前所有寄存器信息
![图片丢失](https://github.com/waxnkw/OScourse/raw/master/resource/img/registersInfo.PNG)

<code>info reg eax</code>  只显示一个寄存器信息(如eax)

<code>next</code>执行到下一次断点

<code>x/<n/f/u> address</code> 显示由内存单元address开始的,格式为f的,每个元素长度为u的,的n个元素的内容
>>f: x 按十六进制格式显示变量。  
d 按十进制格式显示变量。  
u 按十六进制格式显示无符号整型。  
o 按八进制格式显示变量。  
t 按二进制格式显示变量。  
a 按十六进制格式显示变量。  
c 按字符格式显示变量。  
f 按浮点数格式显示变量。  

u:b表示单字节，h表示双字节，w表示四字 节，g表示八字节

例子: <code>x/64cb 0x445455</code> 显示以0x445455为起始地址的内容
![图片丢失](https://github.com/waxnkw/OScourse/raw/master/resource/img/nasm1.PNG)