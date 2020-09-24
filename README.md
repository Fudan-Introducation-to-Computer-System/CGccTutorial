# C & GCC
> 上节课——CGdbTutorial中，我们又补充了一些C语言的知识，这次，我们再简单讲一下malloc和free。这样整个C语言的主体就出来了，同学们可以根据这个框架，继续深入学习C语言。最近，实验室组里有同学在做C语言的库分析，其中就需要用到我们这门课学的汇编代码内容，比如lea 0x157c0(%rip), %rdi。如果大家要编程，很多东西其实是互通的，这是一门完整互通的科学，你一门课学得好，肯定能让你在相关的其他课程也学得好。 —— 谢东方

## malloc & free
C语言中需要手动进行内存的管理，因为调用方法时，方法内部的局部变量放在栈上，如果栈消失了，数据也就消失了。这不是我们想要的结果，为了让数据的生存周期更长，我们使用malloc在堆上分配变量所需的空间。malloc函数放在stdlib.h头文件中，需要我们进行导入。malloc(size_t size)在堆上分配了size大小的空间后，返回它的指针。但是注意，有一个malloc就要有一个free，对空间进行释放。否则，如果程序运行了很久，很多内存没有释放，你的内存就不够用了，造成内存泄漏的情况。下面是一个例子。

```C
#include <stdio.h>
#include <stdlib.h>
struct ListNode* getNodeFromStack();
struct ListNode* getNodeFromHeap();

struct ListNode {
    int val;
    struct ListNode* next;
};

int main(int argc, char** argv) {
    struct ListNode* stackNode = getNodeFromStack();
    struct ListNode* heapNode = getNodeFromHeap();
    printf("val of node from heap: %d\n", heapNode->val);
    free(heapNode);
    printf("val of node from stack: %d\n", stackNode->val);
    return 0;
}

struct ListNode* getNodeFromStack() {
    struct ListNode node;
    node.val = 10;
    node.next = NULL;
    return &node;
}

struct ListNode* getNodeFromHeap() {
    struct ListNode* node = (struct ListNode*)malloc(sizeof(struct ListNode));
    node->val = 20;
    node->next = NULL;
    return node;
}

```
思考：
1. gcc编译给了你什么信息，这可能导致什么问题？
2. 为什么会出现Segmentation fault？


## 函数
C语言的函数其实和java的差不多，但是C不同的地方在于，你必须提前声明你的函数，才能够使用。比如下面这种情况，无法通过编译，因为编译器不知道hello方法在哪里。
```c
int main(int argc, char** argv) {
    hello();
}
void hello() {

}
```
正确的做法：
```c
void hello(); // 在最前面放一个函数声明。
int main(int argc, char** argv) {
    hello();
}
void hello() {

}
```



## GCC
gcc是C语言的编译器（GNU C Compiler），帮助我们编译C程序。接下来以hello.c为例。参考链接： https://blog.csdn.net/men_wen/article/details/75200430

```c
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


C语言的基础探索就先到这里了，接下来可能会更注重课本上的知识。