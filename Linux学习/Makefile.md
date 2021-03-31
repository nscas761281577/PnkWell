# Makefile学习笔记
## Makefile是什么
`Makefile`是为了在大型项目的开发过程中能更加科学规范的编译和生成而催生出来的技术。在我理解里，这是一个脚本语言，搭配`make`程序使用，能有效提高项目在开发过程中的生成可执行文件的效率。

在`Makefile`文件里面书写脚本语言是这种技术的应用方式。编码人员或者项目管理员在`Makefile`中书写`make`操作的过程和过程中所需要的各个文件依赖，在进行`make`操作时，`make`程序会按照`Makefile`文件中所写操作执行，并且会按照其中的依赖关系寻找`make`程序自己需要的文件，类似`Windows`中的批处理文件一样。而项目修改时，程序员仅需修改一小部分甚至无需修改，以此达到全自动编译大型项目的效果，极大提高了开发效率，这便是`Makefile`。

Window的Visual Studio已经替你完成了自动生成Makefile的任务，但是作为一个程序员，熟练使用Makefile是一项非常重要的技能。

## Makefile语法规范和核心原则

在`Makefile`文件里面主要写的是让`make`程序的一条条规则。下面是一条规则示例。

```makefile
main:main.cpp function.cpp function.h

    ${CXX} -c main.cpp function.cpp
```

这条规则表示使用g++编译器将`main.cpp`、`function.cpp`编译成同名目标文件`main.o`、`function.o`.

`Makefile`规则的基本语法如下：

```makefile
target... : prerequisites...
    command
    ...
```

- target——目标文件|执行文件|标签
- prerequisites——该target所依赖的文件和/或target
- command——任意的shell命令，注意该行需要以一个`tab`制表符或者四个空格开头

**只要`prerequisites`里面的一个或者多个文件的修改时间比`target`里面的文件时间要更新，那么`command`里面的命令就会执行。(下文统称核心原则)**
## 从一个简单的示例入手

上面我们已经介绍了Makefile的主要原则或者说核心，下面是`Makefile`的详细说明。

```makefile
edit : main.o kbd.o command.o display.o \
insert.o search.o files.o utils.o
    cc -o edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
 
main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
```

上面代码是一个生成`edit`可执行程序的一段`makefile`代码，目标(`target`)为`edit`可执行文件和各种目标文件(`*.o`),所需要的依赖(`prerequisite`)便是冒号后面的各种文件。其中的反斜杠(`\`)的主要作用是当代码段太长时连接两行代码，便于阅读也有美观的作用。紧接着那一行是生成目标文件的操作系统代码，就和直接在`shell`或者控制台中输入的代码类似。make不会管命令是如何执行的，它只会直接执行命令，相当于直接将代码输入控制台一样。然后`make`按照核心原则比较依赖文件和目标文件的日期，来决定是否执行命令。

注意最后的`clean`不是一个目标文件，它只是一个动作的名字。其冒号后面什么都没有，那么它就不会自动的寻找它的依赖关系，也不会自动执行后面所跟的命令。要执行其命令，就要在`make`命令之后显式的指出这个动作的名字，如
```shell
    make clean
```
这个功能使得我们可以在`makefile`中定义一些我们自己的操作，而又不会影响`makefile`的正常使用，比如文件的备份、删除或者程序的打包等等，使得我们的效率非常高。

## Makefile的工作流程

从上面的例子来说，在`Makefile`所在根目录的控制台或者`shell`中输入`make`命令，`make`程序会在当前目录寻找`Makefile`文件；找到之后寻找其中所写的第一个目标文件，也就是`edit`，若未找到或者是依赖文件比目标文件`edit`新，则执行后面的生成命令，若依赖文件未找到，则寻找该依赖文件的依赖文件，以此类推，直到所有`edit`的依赖文件生成都找到，执行后面的生成命令。

## 变量

在上述例子中我们可以看到`*.o`字符串被使用了多次，在书写的过程中这会影响工作效率，因此`Makefile`中允许定义变量来简化重复的字符串书写。定义格式如下：

```makefile
    OBJECTS = main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
```

那么在在使用过程中我们就可以以${OBJECTS}的方式来使用这个变量了，如下：

```makefile
    edit : ${OBJECTS}
        cc -c edit ${OBJECTS}
```

同理下方的`clean`命令可以写成：
```makefile
    clean :
        rm edit ${OBJECTS}
```