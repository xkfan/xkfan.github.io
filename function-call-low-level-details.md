# Ｃ语言中直接函数调用和间接函数调用的底层调用方式 X86 & AArch64

总结一下X86和AArch64架构下Ｃ语言中直接函数调用和间接函数调用的底层调用方式。
主要是想记录一下在机器码这个层面直接函数调用和间接函数调用如何获取目标函数的地址。

## １.直接函数调用
话不多说，先上C代码

```c
// test.c
＃include <stdio.h>
void foo() {
  printf("function foo\n");
}
int main(int argc, char **argv)
{
  foo();
  return 0;
}
```
编译

```bash
gcc -O0 -o ex test.c
```
反汇编

```bash
objdump -d ex &> out.txt
```
### 1.1 X86
X86的反汇编代码

![Image of x86 direct](https://xkfan.github.io/images/function-call-low-level-details/x86-direct-crop-edited.png)

红框选中的就是直接函数调用的反汇编代码。
直接函数调用通过相对偏移来寻址，e8代表callq指令，d6ffffff表示跳转的地址，intel采用小端寻址，所以d6ffffff表示的值是0xffffffd6，
计算机内负数通过补码表示，补码表示的绝对值是取反加一，所以0xffffffd6表示的绝对值是0x2a，
0x2a正好是函数foo的第一条指令的地址4004b2和callq的下一条指令的地址4004dc之间的差。
所以x86结构下，直接函数调用的寻址方式是目标函数的第一条指令的地址与callq指令下一条指令的地址之间的相对偏移。

### 1.2 AArch64
AArch64的反汇编代码

![Image of x86 direct](https://xkfan.github.io/images/function-call-low-level-details/arm-direct-crop-edited.png)

ARM AArch64的指令都是４字节对齐，bl指令的的前6位是操作码，后26位是立即数地址，红框中指令中立即数为0x3fffff4，
0x3fffff4表示的绝对值为0xc，ARM AArch64通过该立即数x4表示寻址的地址，0xc x 4 = 0x30，
是目标函数foo的第一条指令的地址4005a4和当前bl指令的地址4005d4之间的差。
所以ARM AArch64结构下，直接函数调用的寻址方式是目标函数的第一条指令的地址与bl指令的地址之间的相对偏移。

## ２.间接函数调用
C代码

```c
// test.c
#include <stdio.h>
void foo() {
  printf("function foo\n");
}
int main(int argc, char **argv)
{
  void (*fp)() = foo;
  fp();
  return 0;
}
```
编译

```bash
gcc -O0 -o ex test.c
```
反汇编

```bash
objdump -d ex &> out.txt
```

### 2.1 X86
X86的反汇编代码

![Image of x86 direct](https://xkfan.github.io/images/function-call-low-level-details/x86-indirect-crop-edited.png)

X86结构下的间接函数调用通过绝对地址直接寻址。
第一个红框中的指令将目标函数foo的第一条指令的地址4004b2写入栈中，即赋值给函数指针变量fp。
第二个红框中的第一条mov指令将存放在函数指针fp中的目标函数第一条指令的地址读入寄存器rdx中，
然后callq指令通过存放在寄存器rdx中的绝对地址跳转到目标函数运行。

### 2.2 AArch64
ARM AArch64的反汇编代码

![Image of x86 direct](https://xkfan.github.io/images/function-call-low-level-details/arm-indirect-crop-edited.png)

ARM AArch64结构下的间接函数调用也是通过绝对地址直接寻址。
第一个红框中的指令首先通过程序空间起始地址400000和目标函数foo第一条指令相对起始地址的偏移5a4相加得到目标函数第一条指令的地址4005a4，
然后将该地址写入栈中，即赋值给函数指针变量fp。
第二个红框中的第一条mov指令将存放在函数指针fp中的目标函数第一条指令的地址读入寄存器x0中，
然后blr指令通过存放在寄存器x0中的绝对地址跳转到目标函数运行。

## 3.总结
直接函数调用都是通过相对偏移寻址，X86和ARM AArch64的区别在于:
X86的偏移是目标函数的首地址与call指令下一条指令地址之间的相对偏移，而ARM AArch64的偏移是目标函数的首地址与call指令本身地址之间的相对偏移。
间接函数调用都是通过绝对地址直接寻址，X86和ARM AArch64相同。
