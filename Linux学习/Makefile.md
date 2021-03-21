# Makefile学习笔记
## Makefile是什么
`Makefile`是为了在大型项目的开发过程中能更加科学规范的编译和生成而催生出来的技术。在我理解里，这是一个脚本语言，搭配`make`程序使用，能有效提高项目在开发过程中的生成可执行文件的效率。

在`Makefile`文件里面书写脚本语言是这种技术的应用方式。编码人员或者项目管理员在`Makefile`中书写`make`操作的过程和过程中所需要的各个文件依赖，在进行`make`操作时，`make`程序会按照`Makefile`文件中所写操作执行，并且会按照其中的依赖关系寻找`make`程序自己需要的文件，类似`Windows`中的批处理文件一样。而项目修改时，程序员仅需修改一小部分甚至无需修改，以此达到全自动编译大型项目的效果，极大提高了开发效率，这便是`Makefile`。

Window的Visual Studio已经替你完成了自动生成Makefile的任务，但是作为一个程序员，熟练使用Makefile是一项非常重要的技能。

## Makefile语法规范

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
- command——任意的shell命令

**只要prerequisites里面的一个或者多个文件的修改时间比target里面的文件时间要更新，那么comman里面的命令就会执行。**