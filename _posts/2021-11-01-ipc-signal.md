---
layout: post
title: linux进程间通信-信号
categories: LinuxIPC
description: linux进程间通信-信号。
keywords: linux, 进程间通信
---

# linux进程间通信（三）：信号

## 1.信号？纳尼？

windows中都知道可以用任务管理器强制结束某个进程，linux中也可以通过生成信号和捕获信号来实现的，运行中的进程捕获到这个信号然后作出一定的操作并最终被终止。

信号是UNIX和Linux系统响应某些条件而产生的一个事件，接收到该信号的进程会相应地采取一些行动。通常信号是由一个错误产生的。但它们还可以作为进程间通信或修改行为的一种方式，明确地由一个进程发送给另一个进程。一个信号的产生叫生成，接收到一个信号叫捕获。

## 2.信号的种类

信号的名称是在头文件signal.h中定义的，信号都以SIG开头，常用的信号并不多，常用的信号如下：

- 

- 

- 

- 

更多的信号类型可以查看附录表

## 3. 信号的处理--signal（）函数

程序可使用`signal()`函数来处理指定的信号，主要通过忽略和恢复其默认行为来工作。signal()函数的原型如下：

```c
#include <signal.h>
void (*signal(int sig, void (*func)(int))) (int);
```

这个声明比较复杂，需要仔细点看。

可以知道`signal`是一个带有`sig`和`func`两个参数的函数;

`func`是一个类型为`void (*)(int)`的函数指针。

该函数返回一个与`func`相同类型的指针，指向先前指定信号处理函数的函数指针。准备捕获的信号的参数由`sig`给出，接收到的指定信号后要调用的函数由参数`func`给出。其实这个函数的使用是相当简单的，通过下面的例子就可以知道。注意信号处理函数的原型必须为`void func（int）`，或者是下面的特殊值：

```bash
SIG_IGN : 忽略信号
SIG_DFL : 恢复信号的默认行为
```

给出一个例子来说明一下吧，源文件名为 signal1.c，代码如下：

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>
 
void ouch(int sig)
{
    printf("\nOUCH! - I got signal %d\n", sig);
     
    // 恢复终端中断信号SIGINT的默认行为
    (void) signal(SIGINT, SIG_DFL);
}
 
int main()
{
    // 改变终端中断信号SIGINT的默认行为，使之执行ouch函数
    // 而不是终止程序的执行
    (void) signal(SIGINT, ouch);
    while (1) {
        printf("Hello World!\n");
        sleep(1);
    }
 
    return 0;
}
```

运行结果如下：

```bash
//todo
```

可以看到，第一次按下终止命令`（ctrl+c）`时，进程并没有被终止，面是输出`OUCH! - I got signal 2`，因为`SIGINT`的默认行为被`signal()`函数改变了，当进程接受到信号`SIGINT`时，它就去调用函数`ouch`去处理，注意`ouch`函数把信号`SIGINT`的处理方式改变成默认的方式，所以当你再按一次`ctrl+c`时，进程就像之前那样被终止了。

## 4.信号处理--sigaction（）函数

前面我们看到了signal()函数对信号的处理，但是一般情况下我们可以使用一个更加健壮的信号接口 —— `sigaction()`函数。它的原型为：

```c
#include <signal.h>

int sigaction(int sig, const struct sigaction *act, struct sigaction *oact);
```

该函数与`signal()`函数一样，用于设置与信号`sig`关联的动作，而`oact`如果不是空指针的话，就用它来保存原先对该信号的动作的位置，`act`则用于设置指定信号的动作。

`sigaction`结构体定义在`signal.h`中，但是它至少包括以下成员：

```bash
void (*) (int) sa_handler：处理函数指针，相当于signal函数的func参数。

sigset_t sa_mask： 指定一个。信号集，在调用sa_handler所指向的信号处理函数之前，该信号集将被加入到进程的信号屏蔽字中。信号屏蔽字是指当前被阻塞的一组信号，它们不能被当前进程接收到

int sa_flags：信号处理修改器;
```

`sa_mask` 的值通常是通过使用信号集函数来设置的，关于信号集函数，后续再详细讲述。

`sa_flags`，通常可以取以下的值：

- SA_NOCLDSTOP：子进程停止时不产生SIGCHLD信号。
- SA_RESETHAND：将对此信号的处理方式再信号处理函数的入口处重置为
- SA_RESTART：重启可终端的函数而不是给出EINTER错误
- SA_NODEFER： 捕获到信号时不将它添加到信号屏蔽字中

此外，现在有一个这样的问题，我们使用signal()或sigaction()函数来指定处理信号的函数，但是如果这个信号处理函数建立之前就接收到要处理的信号的话，进程会有怎样的反应呢？它就不会像我们想像的那样用我们设定的处理函数来处理了。sa_mask就可以解决这样的问题，sa_mask指定了一个信号集，在调用sa_handler所指向的信号处理函数之前，该信号集将被加入到进程的信号屏蔽字中，设置信号屏蔽字可以防止信号在它的处理函数还未运行结束时就被接收到的情况，即使用sa_mask字段可以消除这一竞态条件。

承接上面的例子，下面给出用sigaction()函数重写的例子代码，源文件为signal2.c，代码如下：

```c
#include <unistd.h>
#include <stdio.h>
#include <signal.h>
 
void ouch(int sig)
{
    printf("\nOUCH! - I got signal %d\n", sig);
}
 
int main()
{
    struct sigaction act;
    act.sa_handler = ouch;
     
    // 创建空的信号屏蔽字，即不屏蔽任何信息
    sigemptyset(&act.sa_mask);
     
    // 使sigaction函数重置为默认行为
    act.sa_flags = SA_RESETHAND;
 
    sigaction(SIGINT, &act, 0);
 
    while(1)
    {
        printf("Hello World!\n");
        sleep(1);
    }
 
    return 0;
}
```

运行结果与前一个例子中的相同。注意`sigaction`函数在默认情况下是不被重置的，如果要想它重置，则sa_flags就要为`SA_RESETHAND`。

## 5.发送信号

上面说到的函数都是一些进程接收到一个信号之后怎么对这个信号作出反应，即信号的处理的问题，有没有什么函数可以向一个进程主动地发出一个信号呢？我们可以通过两个函数kill()和alarm()来发送一个信号。

### 1.kill函数

先来看看kill()函数，进程可以通过kill()函数向包括它本身在内的其他进程发送一个信号，如果程序没有发送这个信号的权限，对kill()函数的调用就将失败，而失败的常见原因是目标进程由另一个用户所拥有。想一想也是容易明白的，你总不能控制别人的程序吧，当然超级用户root，这种上帝般的存在就除外了。

kill()函数的原型为：

```c
#include <sys/types.h>
#include <signal.h>
int kill(pid_t pid, int sig);
```

它的作用把信号sig发送给进程号为pid的进程，成功时返回0。

kill()调用失败返回-1，调用失败通常有三大原因：

```bash
1、给定的信号无效（errno = EINVAL)
2、发送权限不够( errno = EPERM ）
3、目标进程不存在( errno = ESRCH )
```

### 2.alarm（）函数

这个函数跟它的名字一样，给我们提供了一个闹钟的功能，进程可以调用alarm()函数在经过预定时间后向发送一个SIGALRM信号。

alarm()函数的原型如下：

```c
#include <unistd.h>
unsigned int alarm(unsigned int seconds);
```

alarm()函数用来在seconds秒之后安排发送一个SIGALRM信号，如果seconds为0，将取消所有已设置的闹钟请求。alarm()函数的返回值是以前设置的闹钟时间的余留秒数，如果返回失败返回-1。

马不停蹄，下面就给合fork()、sleep()和signal()函数，用一个例子来说明kill()函数的用法吧，源文件为signal3.c，代码如下：

```c
#include <unistd.h>
#include <sys/types.h>
#include <stdlib.h>
#include <stdio.h>
#include <signal.h>
 
static int alarm_fired = 0;
 
void ouch(int sig)
{
    alarm_fired = 1;
}
 
int main()
{
    pid_t pid;
    pid = fork();
    switch(pid)
    {
    case -1:
        perror("fork failed\n");
        exit(1);
    case 0:
        // 子进程
        sleep(5);
         
        // 向父进程发送信号
        kill(getppid(), SIGALRM);
        exit(0);
    default:;
    }
     
     // 设置处理函数
    signal(SIGALRM, ouch);
    while(!alarm_fired)
    {
        printf("Hello World!\n");
        sleep(1);
    }
    if(alarm_fired)
        printf("\nI got a signal %d\n", SIGALRM);
 
    exit(0);
}
```

运行结果如下：

```bash

```

在代码中我们使用fork()调用复制了一个新进程，在子进程中，5秒后向父进程中发送一个SIGALRM信号，父进程中捕获这个信号，并用ouch()函数来处理，变改alarm_fired的值，然后退出循环。从结果中我们也可以看到输出了5个Hello World！之后，程序就收到一个SIGARLM信号，然后结束了进程。

注：如果父进程在子进程的信号到来之前没有事情可做，我们可以用函数pause()来挂起父进程，直到父进程接收到信号。当进程接收到一个信号时，预设好的信号处理函数将开始运行，程序也将恢复正常的执行。这样可以节省CPU的资源，因为可以避免使用一个循环来等待。以本例子为例，则可以把while循环改为一句pause();

下面再以一个小小的例子来说明alarm函数和pause函数的用法吧，源文件名为，signal4.c，代码如下：

```c
#include <unistd.h>
#include <sys/types.h>
#include <stdlib.h>
#include <stdio.h>
#include <signal.h>
 
static int alarm_fired = 0;
 
void ouch(int sig)
{
    alarm_fired = 1;
}
 
int main()
{
    // 关联信号处理函数
    signal(SIGALRM, ouch);
     
    // 调用alarm函数，5秒后发送信号SIGALRM
    alarm(5);
     
    // 挂起进程
    pause();
     
    // 接收到信号后，恢复正常执行
    if(alarm_fired == 1)
    {
        printf("Receive a signal %d\n", SIGALRM);
    }
 
    exit(0);
}
```

运行结果如下：

```c

```

进程在5秒后接收到一个SIGALRM，进程恢复运行，打印信息并退出。

## 6.信号处理函数的安全问题

试想一个问题，当进程接收到一个信号时，转到你关联的函数中执行，但是在执行的时候，进程又接收到同一个信号或另一个信号，又要执行相关联的函数时，程序会怎么执行？

也就是说，信号处理函数可以在其执行期间被中断并被再次调用。当返回到第一次调用时，它能否继续正确操作是很关键的。这不仅仅是递归的问题，而是可重入的（即可以完全地进入和再次执行）的问题。而反观Linux，其内核在同一时期负责处理多个设备的中断服务例程就需要可重入的，因为优先级更高的中断可能会在同一段代码的执行期间“插入”进来。

简言之，就是说，我们的信号处理函数要是可重入的，即离开后可再次安全地进入和再次执行，要使信号处理函数是可重入的，则在信息处理函数中不能调用不可重入的函数。下面给出可重入的函数在列表，不在此表中的函数都是不可重入的，可重入函数表如下：

（图：TODO）