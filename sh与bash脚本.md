# #! /bin/bash

## What is shell script?

shell script其实就是一组可以被一起执行的shell command，这个shell script可以被写在一个`.sh`文件中，也可以被写在一个text文件中。

shell script需要被UNIX/UNIX-like `sh` `执行`，这里的sh就被称作`command line interpreter（命令行翻译员）`



## What is shell?

shell是一个介于用户和OS之间的interface（接口），专门用来帮助用户访问操作系统。

<img src="https://upload-images.jianshu.io/upload_images/3143224-6ddf32ceac5f8f8b.png?imageMogr2/auto-orient/strip|imageView2/2/w/974/format/webp" alt="img" style="zoom:67%;" />



## What is sh、bash、zsh?

刚刚提到sh是解释器、是shell command的执行者，根据上图可以看出，shell中的功能基本是由sh实现的。

但是sh只是一种规范，定义了sh必须有的功能和规约。然而作为规范的sh必须就有真正的实现：

通常我们常见的`/bin/sh`就是sh实现的软连接。
 你可能也会经常看到`/bin/bash`，看起来像是bash实现的软连接，那么bash是什么？

bash开始的时候其实是sh的一种实现，但是随着需求越来越多，bash中又加入很多sh中并没有包含的功能，甚至bash中有一些行为会更改sh中的需求。因此并不能完全的说bash是sh的实现。

当然zsh也是解释器，只不过是比bash功能更加强大的解释器。



经常出现在shell script file第一行的这段代码到底是什么意思呢？

```sh
#! /bin/bash
#! /bin/sh
```

第一行是告诉操作系统，使用`#! /bin/bash`，使用这个路径下的sh实现来执行下面的shell scirpt

第二行是告诉操作系统，使用`#! /bin/sh`，使用这个路径下的sh实现来执行下面的shell scirpt

这第一行代码通常被称为`hashbang`或`shebang`





## #! /bin/bash -ex

我们知道了这行`hashbang`的作用，那么参数是`-ex`的作用是什么呢？



```sh
#! /bin/bash -ex
```

- `-e`: 如果shell command中的任何一行failed，整个shell script file的运行会在这个command处立刻终止。
- `-x`: 在shell script的执行过程中，将command以及参数全部在标准输出中console出来

更多的sh参数，你可以使用`bash -c "help set"` 查阅

tiger@user-G560-V5:~/xjc_chess/KataGoTF2MacOS/TestRun/scripts/train/dated/20210512-163839$ bash -c "help set"
set: set [--abefhkmnptuvxBCHP] [-o 选项名] [--] [参数 ...]
    设定或取消设定 shell 选项和位置参数的值。

    改变 shell 选项和位置参数的值，或者显示 shell 变量的
    名称和值。
    
    选项：
      -a  标记修改的或者创建的变量为导出。
      -b  立即通告任务终结。
      -e  如果一个命令以非零状态退出，则立即退出。
      -f  禁用文件名生成(模式匹配)。
      -h  当查询命令时记住它们的位置
      -k  所有的赋值参数被放在命令的环境中，而不仅仅是
          命令名称之前的参数。
      -m  启用任务控制。
      -n  读取命令但不执行
      -o 选项名
          设定与选项名对应的变量：
              allexport    与 -a 相同
              braceexpand  与 -B 相同
              emacs       使用 emacs 风格的行编辑界面
              errexit      与 -e 相同
              errtrace     与 -E 相同
              functrace    与 -T 相同
              hashall      与 -h 相同
              histexpand   与 -H 相同
              history      启用命令历史
              ignoreeof    shell 读取文件结束符时不会退出
              interactive-comments
                           允许在交互式命令中显示注释
              keyword      与 -k 相同
              monitor      与 -m 相同
              noclobber    与 -C 相同
              noexec       与 -n 相同
              noglob       与 -f 相同
              nolog        目前可接受但是被忽略
              notify       与 -b 相同
              nounset      与 -u 相同
              onecmd       与 -t 相同
              physical     与 -P 相同
              pipefail     管道的返回值是最后一个非零返回值的命令的返回结果，
                           或者当所有命令都返回零是也为零。
              posix        改变默认时和 Posix 标准不同的 bash 行为
                           以匹配标准
              privileged   与 -p 相同
              verbose      与 -v 相同
              vi           使用 vi 风格的行编辑界面
              xtrace       与 -x 相同
      -p  无论何时当真实的有效的用户身份不匹配时打开。
          禁用对 $ENV 文件的处理以及导入 shell 函数。
          关闭此选项会导致有效的用户编号和组编号设定
          为真实的用户编号和组编号
      -t  读取并执行一个命令之后退出。
      -u  替换时将为设定的变量当作错误对待。
      -v  读取 shell 输入行时将它们打印。
      -x  执行命令时打印它们以及参数。
      -B  shell 将执行花括号扩展。
      -C  设定之后禁止以重定向输出的方式覆盖常
          规文件。
      -E  设定之后 ERR 陷阱会被 shell 函数继承。
      -H  启用 ! 风格的历史替换。当 shell 是交互式的
          时候这个标识位默认打开。
      -P  设定之后类似 cd 的会改变当前目录的命令不
          追踪符号链接。
      -T  设定之后 DEBUG 陷阱会被 shell 函数继承。
      --  任何剩余的参数会被赋值给位置参数。如果没
          有剩余的参数，位置参数不会被设置。
      -   任何剩余的参数会被赋值给位置参数。
          -x 和 -v 选项已关闭。
    
    使用 + 而不是 - 会使标志位被关闭。标志位也可以在
    shell 被启动时使用。当前的标志位设定可以在 $- 变
    量中找到。剩余的 ARG 参数是位置参数并且是按照
    $1, $2, .. $n 的顺序被赋值的。如果没有给定 ARG
    参数，则打印所有的 shell 变量。



# shell 中 [-eq] [-ne] [-gt] [-lt] [ge] [le]

-eq      //等于

-ne      //不等于

-gt      //大于

-lt      //小于

ge      //大于等于

le      //小于等于





# shell shift命令用法

shift命令用于对参数的移动(左移)，通常用于在不知道传入参数个数的情况下依次遍历每个参数然后进行相应处理（常见于Linux中各种程序的启动脚本）。

示例：依次读取输入的参数并打印参数个数
shift_test.sh

```bash
#!/bin/bash
while [ $# != 0 ]
do
echo "prama is $1,prama size is $#"
shift
done
```



输入如下命令运行：



```swift
./shift_test.sh a b c
prama is a,prama size is 3
prama is b,prama size is 2
prama is c,prama size is 1
```



# Shell脚本bash: /bin/bash^M：解释器错误: 没有那个文件或目录 -- 报错

有时候编写脚本时会出现类似标题列出的错误，这个问题大多数是因为你的脚本文件在windows下编辑过。

windows下，每一行的结尾是\n\r，而在linux下文件的结尾是\n，那么你在windows下编辑过的文件在linux下打开看的时候每一行的结尾就会多出来一个字符\r,用cat -A yourfile时你可以看到这个\r字符被显示为^M，

这时候只需要删除这个字符就可以了。可以使用命令
![image-20220302113358918](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220302113358918.png)

![image-20220302113242845](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220302113242845.png)

![image-20220302113504161](C:\Users\jc\AppData\Roaming\Typora\typora-user-images\image-20220302113504161.png)







# shell time命令

time命令最常用的使用方式就是在其后面直接跟上命令和参数：

time <command> [<arguments...>]

在命令执行完成之后就会打印出CPU的使用情况：

real  0m5.064s   <== 实际使用时间（real time） 
user  0m0.020s   <== 用户态使用时间（the process spent in user mode） 
sys   0m0.040s   <== 内核态使用时间（the process spent in kernel mode）





# shell  @X  特殊变量



| 变量 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| $0   | 当前脚本的文件名                                             |
| $n   | 传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。 |
| $#   | 传递给脚本或函数的参数个数。                                 |
| $*   | 传递给脚本或函数的所有参数。                                 |
| $@   | 传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同，下面将会讲到。 |
| $?   | 上个命令的退出状态，或函数的返回值。                         |
| $$   | 当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。 |



## $* 和 $@ 的区别

$* 和 $@ 都表示传递给函数或脚本的所有参数，不被双引号(" ")包含时，都以**"$1"    "$2"   …   "$n"**   的形式输出所有参数。

但是当它们被双引号(" ")包含时，"$*" 会将所有的参数作为一个整体，以**"$1 $2 … $n"**的形式输出所有参数；

"$@" 会将各个参数分开，以"$1" "$2" … "$n" 的形式输出所有参数。



$? 可以获取上一个命令的退出状态。所谓退出状态，就是上一个命令执行后的返回结果。

退出状态是一个数字，一般情况下，大部分命令执行成功会返回 0，失败返回 1。



# shell命令之tee命令

#### tee命令：

在输出信息的同时把信息记录到文件中
