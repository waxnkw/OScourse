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
![图片丢失]("../resource/img/breakInfo.PNG")

<code>bt</code>  显示当前栈数据

<code>info registers</code>  显示当前所有寄存器信息
![图片丢失]("../resource/img/registersInfo.PNG")

<code>info registers eax</code>  只显示一个寄存器信息(如eax)
