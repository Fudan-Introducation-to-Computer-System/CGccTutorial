# C & GCC
> 上节课——CGdbTutorial中，我们又补充了一些C语言的知识，这次，我们再简单讲一下malloc和free。这样整个C语言的主体就出来了，同学们可以根据这个框架，继续深入学习C语言。最近，实验室组里有同学在做C语言的库分析，其中就需要用到我们这门课学的汇编代码内容，比如lea 0x157c0(%rip), %rdi。如果大家要编程，很多东西其实是互通的，这是一门完整互通的科学，你一门课学得好，肯定能让你在相关的其他课程也学得好。 —— 谢东方

## malloc & free
### malloc




## GCC
gcc是C语言的编译器（GNU C Compiler），帮助我们编译C程序。接下来以hello.c为例。参考链接： https://blog.csdn.net/men_wen/article/details/75200430

```
#include <stdio.h>
int main(int argc, char** argv) {
    printf("hello, c!\n");
    return 0;
}
```
### 一步到位
1. 一步到位生成可执行文件。
    ```
    gcc hello.c -o hello
    ```
通过gcc对hello.c进行编译，-o hello指定output文件为hello。

运行通过以下命令即可。
    ```
    ./hello
    ```

### 多步生成可执行文件
1. 预处理
   将一些宏定义的内容进行处理，将头文件导入到C文件中，虽然这个时候还是C文件，但是为了加以区分，我们将它命名为.i文件。
   ```
    gcc -E hello.c -o hello.i
   ```

2. 将C程序转为汇编程序。
    ```
    gcc -S hello.i -o hello.s
    ```
   生成程序如下：
    ```
    main:
    .LFB0:
    .cfi_startproc
    pushq %rbp
    cfi_def_cfa_offset 16
    .cfi_offset 6, -16
    movq  %rsp, %rbp
    ...            
    ```
   你可以看到很多汇编代码，这也是接下来我们要学习的东西。

3. 将汇编程序转为二进制模块。
    ```
    gcc -c hello.s -o hello.o
    ```
    但是，这时生成的.o文件只是一个可执行文件的模块，还需要和其他可执行文件链接才能形成可执行文件。

4. 生成可执行文件。
    ```
    gcc hello.o -o hello
    ```
    之后，通过./hello就可以运行了。


