# 掌握FreeRTOS实时内核

[toc]

## FreeRTOS发行版

### 章节介绍与范围

FreeRTOS以一个包含所有官方FreeRTOS端口和大量预配置演示应用程序的单个zip文件存档形式进行分发。

#### 范围

本章旨在帮助用户通过以下方式熟悉FreeRTOS文件和目录：

- 提供FreeRTOS目录结构的顶层视图。
- 描述哪些文件实际上是特定的FreeRTOS项目所需的。
- 介绍演示应用程序。
- 提供有关如何创建新项目的信息。

这里的描述仅与官方FreeRTOS分发相关。本书附带的示例使用了稍微不同的组织结构。

### 理解FreeRTOS发行版

#### 定义：FreeRTOS端口

FreeRTOS可以使用大约20种不同的编译器构建，并且可以运行在30多种不同的处理器架构上。每个支持的编译器和处理器组合都被视为一个单独的FreeRTOS端口。

#### 构建FreeRTOS

FreeRTOS可以被视为一个库，为本来是裸机应用程序的功能提供多任务能力。

FreeRTOS提供了一组C源文件。其中一些源文件对所有端口都是通用的，而其他一些则特定于一个端口。将这些源文件作为项目的一部分构建，以使FreeRTOS API对您的应用程序可用。为了使这对您变得容易，每个官方的FreeRTOS端口都附带了一个演示应用程序。演示应用程序预配置为构建正确的源文件并且包含正确的头文件。

演示应用程序应该可以“开箱即用”，尽管某些演示应用程序比其他演示应用程序要旧，有时自演示发布以来构建工具的更改可能会引发问题。第1.3节描述了演示应用程序。

#### FreeRTOSConfig.h

FreeRTOS由一个名为FreeRTOSConfig.h的头文件配置。

FreeRTOSConfig.h用于为特定应用程序的使用定制FreeRTOS。例如，FreeRTOSConfig.h包含诸如configUSE_PREEMPTION之类的常量，其设置定义了是使用协作式调度算法还是抢占式调度算法[^1]。由于FreeRTOSConfig.h包含应用程序特定的定义，因此它应该位于构建应用程序的目录中，而不是包含FreeRTOS源代码的目录中。

每个FreeRTOS端口都提供了一个演示应用程序，每个演示应用程序都包含一个FreeRTOSConfig.h文件。因此，从头开始创建FreeRTOSConfig.h文件是不必要的。相反，建议从正在使用的FreeRTOS端口提供的演示应用程序所使用的FreeRTOSConfig.h文件开始，然后进行适应性修改。

[^1]:调度算法在第3.12节中描述。

#### 官方FreeRTOS发行版

FreeRTOS以一个单独的zip文件进行分发。这个zip文件包含了所有FreeRTOS端口的源代码以及所有FreeRTOS演示应用程序的项目文件。它还包含了一些FreeRTOS+生态系统组件和一些FreeRTOS+生态系统演示应用程序。

不要被FreeRTOS分发中的文件数量吓到！在任何一个应用程序中只需要非常少量的文件。

#### FreeRTOS发行版中的顶级目录

FreeRTOS发行版的第一级和第二级目录如图1所示，并进行了描述。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure1.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图1. FreeRTOS发行版中的顶级目录。</div> </center>

这个zip文件只包含一份FreeRTOS源代码文件；所有的FreeRTOS演示项目和FreeRTOS+演示项目都期望在FreeRTOS/Source目录中找到FreeRTOS源代码文件，如果目录结构发生变化，可能导致这些项目无法构建。

#### 适用于所有端口的FreeRTOS源文件

核心的FreeRTOS源代码仅包含在两个C文件中，这两个文件对所有FreeRTOS端口都通用。这些文件分别为tasks.c和list.c，它们位于FreeRTOS/Source目录中，如图2所示。除了这两个文件外，还有以下源文件位于相同的目录下：

- queue.c

  queue.c提供队列和信号量服务，如本书后面所述。通常情况下需要queue.c。

- timers.c

  timers.c提供软件定时器功能，如本书后面所述。只有在实际使用软件定时器时才需要包含它在构建中。

- event_groups.c

  event_groups.c提供事件组功能，如本书后面所述。只有在实际使用事件组时才需要包含它在构建中。

- croutine.c

  croutine.c实现了FreeRTOS协同程序功能。只有在实际使用协同程序时才需要包含它在构建中。协同程序原本是为非常小型的微控制器设计的，现在很少使用，因此没有像其他FreeRTOS功能一样得到同样的维护水平。协同程序不在本书中描述。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure2.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图2. 在FreeRTOS目录树中的核心FreeRTOS源文件。</div> </center>

显然文件名可能会导致名称空间冲突，因为许多项目可能已经包含了具有相同名称的文件。然而，考虑到现在更改文件的名称可能会导致问题，因为这样做会破坏与使用FreeRTOS的成千上万个项目以及自动化工具和IDE插件的兼容性。

#### 特定于端口的FreeRTOS源文件

特定于端口的FreeRTOS源文件位于FreeRTOS/Source/portable目录中。该portable目录按照编译器和处理器架构的层次结构进行排列，如图3所示。

如果您正在运行FreeRTOS，并且使用编译器在具有架构的处理器上运行，那么除了核心FreeRTOS源文件外，您还必须构建位于FreeRTOS/Source/portable/[compiler]/[architecture]目录中的文件。

正如将在第2章中描述的，FreeRTOS还将堆内存分配视为可移植层的一部分。使用早于V9.0.0版本的FreeRTOS的项目必须包含一个堆内存管理器。从FreeRTOS V9.0.0版本开始，只有当在FreeRTOSConfig.h中将configSUPPORT_DYNAMIC_ALLOCATION设置为1时，或者未定义configSUPPORT_DYNAMIC_ALLOCATION时，才需要堆内存管理器。

FreeRTOS提供了五种示例堆内存分配方案。这些方案分别命名为heap_1到heap_5，由源文件heap_1.c到heap_5.c分别实现。这些示例堆内存分配方案位于FreeRTOS/Source/portable/MemMang目录中。如果您已经配置FreeRTOS以使用动态内存分配，那么在您的项目中必须构建这五个源文件中的一个，除非您的应用程序提供了替代实现。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure3.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图3. FreeRTOS目录树中特定于端口的源文件。</div> </center>

#### 包含路径

FreeRTOS需要将三个目录包含在编译器的包含路径中。这些目录是：

1. 核心FreeRTOS头文件的路径，通常为FreeRTOS/Source/include。

2. 用于特定于正在使用的FreeRTOS端口的源文件的路径。如上所述，这是FreeRTOS/Source/portable/[compiler]/[architecture]。

3. FreeRTOSConfig.h头文件的路径。

#### 头文件

使用FreeRTOS API的头文件必须包括‘FreeRTOS.h’，然后是包含正在使用的API函数原型的头文件之一，可以是‘task.h’、‘queue.h’、‘semphr.h’、‘timers.h’或‘event_groups.h’。

### 示例应用程序

每个FreeRTOS端口至少配备有一个演示应用程序，应该能够在构建过程中不生成任何错误或警告，尽管一些演示比其他的要旧，有时自演示发布以来构建工具的变化可能会引发问题。 对于Linux用户的注意事项：FreeRTOS是在Windows主机上开发和测试的。偶尔，当在Linux主机上构建演示项目时，可能会出现构建错误。构建错误几乎总是与引用文件名时使用的字母大小写或文件路径中使用的斜杠字符的方向有关。请使用FreeRTOS联系表单（[http://www.FreeRTOS.org/contact）](http://www.freertos.org/contact）)通知我们此类错误。

演示应用程序有几个目的：

- 提供一个示例，展示一个已配置好并包括正确文件的项目，以及正确的编译器选项设置。
- 允许“开箱即用”的实验，无需进行最小的设置或先前的知识。
- 作为演示FreeRTOS API如何使用的示例。
- 作为创建真实应用程序的基础。

每个演示项目都位于FreeRTOS/Demo目录下的唯一子目录中。子目录的名称指示演示项目所关联的端口。

每个演示应用程序都有一个FreeRTOS.org网站上的网页进行描述。该网页包括以下信息：

- 如何在FreeRTOS目录结构中找到演示项目文件。
- 该项目配置为使用哪些硬件。
- 如何设置硬件以运行演示。
- 如何构建演示。
- 预期演示的行为。

所有演示项目都创建了一组公共演示任务的子集，这些任务的实现包含在FreeRTOS/Demo/Common/Minimal目录中。这些公共演示任务纯粹用于演示FreeRTOS API的使用方式，它们不实现任何特定的有用功能。

较新的演示项目还可以构建一个初学者的“闪烁”项目。闪烁项目非常基础，通常只会创建两个任务和一个队列。

每个演示项目都包含一个名为main.c的文件。其中包含了main()函数，用于创建所有演示应用程序任务。有关特定演示的信息，请参阅各个main.c文件中的注释。

FreeRTOS/Demo目录层次结构如图4所示。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure4.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图4. 示例目录层次结构。</div> </center>

### 创建一个FreeRTOS项目

#### 适配提供的演示项目之一

每个FreeRTOS端口都配备了至少一个预配置的演示应用程序，应该能够在构建过程中不生成任何错误或警告。建议通过适配其中一个现有项目来创建新项目；这将使项目包含正确的文件，正确的中断处理程序，以及正确的编译器选项。

要从现有演示项目开始创建新应用程序，请按照以下步骤进行：

1. 打开提供的演示项目，确保它能够按预期进行构建和执行。
2. 删除定义演示任务的源文件。Demo/Common目录中的任何文件都可以从项目中删除。
3. 删除main()中的所有函数调用，除了如清单1所示的prvSetupHardware()和vTaskStartScheduler()。
4. 检查项目是否仍然能够构建。

按照这些步骤将创建一个包含正确FreeRTOS源文件的项目，但不定义任何功能。

___
```c
int main( void )
{
    /* Perform any hardware setup necessary. */
    prvSetupHardware();
    /* --- APPLICATION TASKS CAN BE CREATED HERE --- */
    /* Start the created tasks running. */
    vTaskStartScheduler();
    /* Execution will only reach here if there was insufficient heap to
    start the scheduler. */
    for( ;; );
    return 0;
}
```
---
<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单1. 新main()函数的模板。</div> </center>

#### 从零开始创建一个新项目

正如前面提到的，建议从现有的演示项目创建新项目。如果这不可行，那么可以使用以下步骤创建新项目：

1. 使用您选择的工具链创建一个尚未包含任何FreeRTOS源文件的新项目。
2. 确保新项目可以构建、下载到目标硬件并执行。
3. 仅当您确信已经有一个可工作的项目时，再将表1中详细列出的FreeRTOS源文件添加到项目中。
4. 将用于所使用端口的演示项目中的FreeRTOSConfig.h头文件复制到项目目录中。
5. 将以下目录添加到项目搜索头文件的路径中：
   - FreeRTOS/Source/include
   - FreeRTOS/Source/portable/[compiler]/[architecture]（其中[compiler]\(编译器)和[architecture]\(架构)是与您选择的端口相对应的正确值）
   - 包含FreeRTOSConfig.h头文件的目录
6. 复制相关演示项目的编译器设置。
7. 安装可能需要的任何FreeRTOS中断处理程序。可以参考描述所使用端口的网页以及所使用端口的演示项目。

| 文件                      | 位置路径                                                     |
| ------------------------- | ------------------------------------------------------------ |
| tasks.c                   | FreeRTOS/Source                                              |
| queue.c                   | FreeRTOS/Source                                              |
| list.c                    | FreeRTOS/Source                                              |
| timers.c                  | FreeRTOS/Source                                              |
| event_groups.c            | FreeRTOS/Source                                              |
| All C and assembler files | FreeRTOS/Source/portable/[compiler]/[architecture]           |
| heap_n.c                  | FreeRTOS/Source/portable/MemMang，其中n可以是1、2、3、4或5。从FreeRTOS V9.0.0开始，此文件变为可选项 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 1. 需要包含在项目中的FreeRTOS源文件 。</div> </center>

使用早于FreeRTOS V9.0.0版本的项目必须构建heap_n.c文件之一。从FreeRTOS V9.0.0开始，只有在FreeRTOSConfig.h中设置configSUPPORT_DYNAMIC_ALLOCATION为1或者未定义configSUPPORT_DYNAMIC_ALLOCATION时，才需要heap_n.c文件。有关更多信息，请参阅第2章《堆内存管理》。

### 数据类型和编码风格指南

#### 数据类型

每个FreeRTOS的端口都有一个独特的portmacro.h头文件，其中包含（除其他外）两个特定于端口的数据类型的定义：TickType_t 和 BaseType_t。这些数据类型在表2中有描述。

| 使用的宏或类型定义 | 实际类型                                                     |
| ------------------ | ------------------------------------------------------------ |
| TickType_t         | FreeRTOS配置了一个称为tick中断的周期性中断。                 |
|                    | 自FreeRTOS应用程序启动以来发生的tick中断数量被称为tick计数。tick计数用作时间度量标准。 |
|                    | 两个tick中断之间的时间称为tick周期。时间以tick周期的倍数来指定。 |
|                    | TickType_t是用于保存tick计数值和指定时间的数据类型。         |
|                    | TickType_t可以是无符号16位类型或无符号32位类型，具体取决于FreeRTOSConfig.h中configUSE_16_BIT_TICKS的设置。如果configUSE_16_BIT_TICKS设置为1，则TickType_t被定义为uint16_t。如果configUSE_16_BIT_TICKS设置为0，则TickType_t被定义为uint32_t。 |
|                    | 在8位和16位体系结构上，使用16位类型可以极大地提高效率，但严重限制了可指定的最大块周期。而在32位体系结构上，没有理由使用16位类型。 |
| BaseType_t         | 这通常被定义为体系结构上最有效的数据类型。通常，在32位体系结构上是32位类型，在16位体系结构上是16位类型，在8位体系结构上是8位类型。 |
|          | BaseType_t通常用于仅能取有限值范围的返回类型，以及pdTRUE/pdFALSE类型的布尔值。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 2. FreeRTOS使用的特定于端口的数据类型。</div> </center>

一些编译器将所有未经限定的char变量声明为无符号的，而其他一些编译器将它们声明为有符号的。因此，FreeRTOS源代码在每次使用char时都会明确加上'signed'或'unsigned'的限定词，除非char用于保存ASCII字符，或者指向字符串的char指针。

普通的int类型不会被使用。

#### 变量名称

变量的前缀是它们的类型：'c'表示char，'s'表示int16_t（short），'l'表示int32_t（long），'x'表示BaseType_t和任何其他非标准类型（结构、任务句柄、队列句柄等）。 如果一个变量是无符号的，它还会加上'u'前缀。如果一个变量是指针，它还会加上'p'前缀。例如，uint8_t类型的变量前缀为'uc'，指向char的指针类型的变量前缀为'pc'。

#### 函数名称

函数的前缀包括它们返回的类型以及它们定义在的文件。例如：

- vTaskPrioritySet() 返回void类型，并定义在task.c中。
- xQueueReceive() 返回BaseType_t类型的变量，并定义在queue.c中。
- pvTimerGetTimerID() 返回指向void的指针，并定义在timers.c中。

文件范围（私有）函数的前缀是'prv'。

#### 格式

一个制表符总是等于四个空格。

#### 宏名称

大多数宏都使用大写字母编写，并以小写字母前缀表示宏的定义位置。表3提供了前缀列表。

| 前缀                                | 宏定义位置                |
| ----------------------------------- | ------------------------- |
| port (例如, portMAX_DELAY)          | portable.h 或 portmacro.h |
| task (例如, taskENTER_CRITICAL())   | task.h                    |
| pd (例如, pdTRUE)                   | projdefs.h                |
| config (例如, configUSE_PREEMPTION) | FreeRTOSConfig.h          |
| err (例如, errQUEUE_FULL)           | projdefs.h                |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 3. 宏的前缀。</div> </center>

请注意，信号量API几乎完全以一组宏的形式编写，但遵循函数命名约定，而不是宏命名约定。

表4中定义的宏在整个FreeRTOS源代码中都有使用。

| 宏      | 实际值 |
| ------- | ------ |
| pdTRUE  | 1      |
| pdFALSE | 0      |
| pdPASS  | 1      |
| pdFAIL  | 0      |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 4. 通用宏定义。</div> </center>

#### 过多类型转换的理由

FreeRTOS源代码可以使用许多不同的编译器进行编译，这些编译器在何时以及如何生成警告方面都有所不同。特别是，不同的编译器要求使用不同的方式进行类型转换。因此，FreeRTOS源代码包含比通常情况下所需的更多类型转换。

## 堆内存管理

### 章节简介和范围

#### 先决条件

FreeRTOS 作为一组 C 源文件提供，成为一名合格的 C 程序员使用 FreeRTOS 的先决条件，本章假设读者熟悉以下概念：

* C 项目是如何构建的，包括不同的编译和链接阶段。
* 栈和堆是什么。
* 标准 C 库 `malloc()` 和 `free()` 函数。

#### 动态内存分配及其与 FreeRTOS 的相关性

本书接下来的章节将介绍内核对象，例如任务、队列、信号量和事件组。 为了使 FreeRTOS 尽可能易于使用，这些内核对象不是在编译时静态分配的，而是在运行时动态分配的；FreeRTOS 在每次创建内核对象时分配 RAM，并在每次删除内核对象时释放 RAM。 该策略减少了设计和规划工作，简化了 API，并且最大限度地减少 RAM 占用空间。

本章讨论动态内存分配。 动态内存分配是C编程概念，而不是特定于 FreeRTOS 或多任务处理的概念。 它与FreeRTOS 相关，因为内核对象是动态分配的，并且通用编译器提供的动态内存分配方案并不总是适用于实时应用程序。

可以使用标准 C 库 `malloc()` 和 `free()` 函数分配内存，但它们由于以下一个或多个原因，可能不适合或不适当：

* 它们并不总是在小型嵌入式系统上可用。
* 它们的实现可能相对较大，占用宝贵的代码空间。
* 它们很少是线程安全的。
* 它们不是确定性的； 执行这些函数所需的时间会因每次调用而异。
* 它们可能受到分片[^2]的影响。
* 它们会使链接器配置复杂化。
* 如果堆空间允许增长到与其他变量使用的内存重叠的区域，它们可能会成为难以调试的错误的根源。

[^2]:分配给程序的内存可能会出现碎片化，即分散在内存中的小块空间，而不是连续的大块空间。这种碎片化可能导致可能会导致无法分配所需大小的内存块，即使总可用空间足够,从而造成内存资源的浪费和性能下降

#### 动态内存分配选项

早期版本的FreeRTOS使用了一种内存池分配方案，其中不同大小的内存块池在编译时预先分配，然后由内存分配函数返回。尽管这在实时系统中是常见的方案，但它被证明是许多技术支持请求的源头，主要因为它不能足够高效地使用RAM，以使其在真正小型嵌入式系统中可行。因此，这个方案被取消了。

FreeRTOS现在将内存分配视为可移植层的一部分（与核心代码基础的一部分相对立）。这是因为不同的嵌入式系统具有不同的动态内存分配和时间要求，因此单一的动态内存分配算法只适用于某些应用程序子集。此外，将动态内存分配从核心代码基础中删除使应用程序编写者可以在适当时提供自己的特定实现。

当FreeRTOS需要RAM时，不使用`malloc()`函数，而是调用`pvPortMalloc()`。当释放RAM时，不使用`free()`函数，内核调用`vPortFree()`。`pvPortMalloc()`具有与标准C库的`malloc()`函数相同的原型，`vPortFree()`具有与标准C库的`free()`函数相同的原型。

`pvPortMalloc()`和`vPortFree()`是公共函数，因此还可以从应用程序代码中调用。

FreeRTOS附带了五个示例实现`pvPortMalloc()`和`vPortFree()`两个函数的方式，所有这些示例都在本章中有详细说明。FreeRTOS应用程序可以使用这些示例中的一个，或者自行实现。

这五个示例分别定义在 heap_1.c、heap_2.c、heap_3.c、heap_4.c 和 heap_5.c 源文件中，这些源文件都位于 FreeRTOS/Source/portable/MemMang 目录下。

#### 范围

本章旨在让读者充分了解以下内容：

- FreeRTOS何时分配RAM。
- FreeRTOS提供的五个示例内存分配方案。
- 选择哪种内存分配方案。

### 内存分配方案示例

#### Heap_1 


对于小型专用嵌入式系统，通常只在调度器启动之前创建任务和其他内核对象。在这种情况下，只有在应用程序开始执行任何实时功能之前，内核才会动态分配内存，并且内存将在应用程序的生命周期内保持分配。这意味着所选择的分配方案不必考虑更复杂的内存分配问题，如确定性和碎片化，而只需考虑代码大小和简单性等属性。

Heap_1.c 实现了一个非常基本的 `pvPortMalloc()` 版本，并没有实现 `vPortFree()`。从不删除任务或其他内核对象的应用程序有可能使用 heap_1。一些商业关键性和安全关键性的系统可能禁止使用动态内存分配，也有可能使用 heap_1。关键性系统通常禁止动态内存分配是因为与不确定性、内存碎片化和分配失败相关的不确定性，但 Heap_1 总是确定性的，不会产生内存碎片。

heap_1 分配方案将一个简单的数组细分为较小的块，当调用 `pvPortMalloc()` 时。这个数组称为FreeRTOS堆。数组的总大小（以字节为单位）由FreeRTOSConfig.h中的`configTOTAL_HEAP_SIZE`定义设置。以这种方式定义一个大数组可以使应用程序在从数组分配任何内存之前就消耗大量RAM。

每个创建的任务都需要从堆中分配任务控制块（TCB）和堆栈。图 5展示了当任务被创建时heap_1如何将简单数组细分。

参考图 5：

1. A显示了在创建任何任务之前的数组状态——整个数组都是空闲的。
2. B显示了在创建一个任务后的数组状态。
3. C显示了在创建三个任务后的数组状态。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure5.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图5. 每次创建任务时从heap_1数组中分配RAM。</div> </center>

#### Heap_2

Heap_2保留在FreeRTOS发行版中以支持向后兼容性，但不建议在新设计中使用它。考虑使用heap_4代替heap_2，因为heap_4提供了增强的功能。

Heap_2.c 也是通过细分一个由`configTOTAL_HEAP_SIZE`确定大小的数组来工作。它使用最佳适配算法来分配内存，并且与heap_1不同，它允许内存被释放。同样，这个数组是静态声明的，因此即使在从数组分配任何内存之前，应用程序看起来也会消耗大量RAM。

最佳适配算法[^3]确保`pvPortMalloc()`使用与请求的字节数最接近的空闲内存块。例如，考虑以下情景：

* 堆中包含三个空闲内存块，分别为5字节、25字节和100字节。
* 调用`pvPortMalloc()`请求20字节的RAM。

可用内存中最小的块并且可以容纳请求的字节数，是25字节的块，因此`pvPortMalloc()`将25字节的块分割为一个20字节的块和一个5字节的块，在返回指向20字节块的指针之前。新的5字节块仍然可供未来的`pvPortMalloc()`调用使用。

[^3]:最佳适配算法（Best Fit Algorithm）是一种用于内存分配的算法，通常在操作系统或内存管理中使用。这个算法的目标是在可用内存块中选择最小的合适块来满足分配请求，以最小化内存浪费。

与heap_4不同，Heap_2不会将相邻的空闲块合并为一个较大的块，因此更容易发生碎片化。然而，如果被分配和随后被释放的块始终是相同大小的，则碎片化不是一个问题。对于一个不断创建和删除任务的应用程序，只要分配给创建的任务的堆栈大小不变，Heap_2是合适的。



<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure6.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图6. 随着任务的创建和删除，RAM从heap_2数组中被分配和释放。</div> </center>

图 6演示了在创建、删除和再次创建任务时最佳适配算法的工作方式。

参考图 6：

1. A显示了在创建三个任务后的数组状态。在数组的顶部仍然有一个大的空闲块。
2. B显示了在其中一个任务被删除后的数组状态。数组顶部的大空闲块仍然存在。现在还有两个较小的空闲块，即之前分配给已删除任务的TCB和堆栈。
3. C 显示了在另一个任务被创建后的情况。创建任务导致了对`pvPortMalloc()`的两次调用，一次用于分配新的TCB，另一次用于分配任务堆栈。任务是使用`xTaskCreate() `API函数创建的，该函数在第3.4节中有描述。对`pvPortMalloc()`的调用在`xTaskCreate()`内部进行。

每个TCB的大小都完全相同，因此最佳适配算法确保以前分配给已删除任务的TCB的内存块被重用以分配新任务的TCB。

分配给新创建任务的堆栈大小与先前删除任务的堆栈大小相同，因此最佳适配算法确保以前分配给已删除任务的堆栈的内存块被重用以分配新任务的堆栈。

数组顶部的较大未分配块保持不变。

Heap_2不是确定性的，但比大多数标准库中的malloc()和free()实现更快。

#### Heap_3

Heap_3.c 使用标准库的 malloc() 和 free() 函数，因此堆的大小由链接器配置定义，configTOTAL_HEAP_SIZE 设置不起作用。

Heap_3通过暂时挂起FreeRTOS调度器来使 malloc() 和 free() 线程安全。线程安全和调度器挂起是在第7章"资源管理"中讨论的主题。

#### Heap_4

与heap_1和heap_2一样，heap_4通过将一个数组细分为较小的块来工作。与以前一样，该数组是静态声明的，由`configTOTAL_HEAP_SIZE`确定尺寸，因此即使在从数组分配任何内存之前，应用程序看起来也会消耗大量RAM。

Heap_4使用首次适应算法[^4]来分配内存。与heap_2不同，heap_4将相邻的空闲内存块合并为一个较大的块（合并），从而最小化内存碎片化的风险。

首次适应算法确保`pvPortMalloc()`使用第一个足够容纳请求的字节数的空闲内存块。例如，考虑以下情景：

- 堆包含三个空闲内存块，按照它们在数组中出现的顺序分别为5字节、200字节和100字节。
- 调用`pvPortMalloc()`请求20字节的RAM。

可以容纳请求的字节数的第一个空闲内存块是200字节的块，因此`pvPortMalloc()`将200字节的块分割为一个20字节的块和一个180字节的块，然后返回指向20字节块的指针。新的180字节块仍然可供未来的`pvPortMalloc()`调用使用。

[^4]:首次适应算法（First Fit Algorithm）是一种用于内存分配的算法，通常用于操作系统或内存管理中。这个算法的原理很简单：当需要分配一块内存时，它会从可用内存块清单中选择第一个足够大的内存块，以满足分配请求的大小。

Heap_4将相邻的空闲块合并为一个较大的块，最小化了碎片化的风险，使其适用于反复分配和释放不同大小的RAM块的应用程序。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure7.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图7. RAM从heap_4数组中被分配和释放的过程。</div> </center>

图 7演示了heap_4的首次适应算法和内存合并在分配和释放内存时的工作方式。

参考图 7：

1. A 显示了在创建三个任务后的数组状态。在数组的顶部仍然有一个大的空闲块。
2. B 显示了在其中一个任务被删除后的数组状态。数组顶部的大空闲块仍然存在。还有一个空闲块，即之前分配给已删除任务的TCB和堆栈。需要注意的是，与heap_2演示时不同，当TCB被删除时释放的内存和堆栈被删除时释放的内存不再保持为两个单独的空闲块，而是合并为一个更大的单一空闲块。
3. C 显示了在创建FreeRTOS队列后的情况。队列是使用`xQueueCreate() `API函数创建的，该函数在第4.3节中有描述。`xQueueCreate()`调用`pvPortMalloc()`来分配队列使用的RAM。由于heap_4使用首次适应算法，`pvPortMalloc()`将从第一个足够容纳队列的空闲RAM块中分配RAM，而在图 7中，这是在任务被删除时释放的RAM。然而，队列并未消耗所有空闲块中的RAM，因此块被分成两部分，未使用的部分仍然可供未来的`pvPortMalloc()`调用使用。
4. D 显示了在直接从应用程序代码中调用`pvPortMalloc()`而不是间接调用FreeRTOS API函数后的情况。用户分配的块足够小，可以适应第一个空闲块，这个空闲块位于队列分配的内存和随后的TCB分配的内存之间。当任务被删除时释放的内存现在已被分为三个单独的块；第一个块包含队列，第二个块包含用户分配的内存，第三个块保持空闲。
5. E 显示了在队列被删除后的情况，这会自动释放已分配给已删除队列的内存。现在在用户分配的块两侧有空闲内存。
6. F 显示了在用户分配的内存也被释放后的情况。之前被用户分配的内存已与两侧的空闲内存合并为一个更大的单一空闲块。

Heap_4不是确定性的，但比大多数标准库中的`malloc()`和`free()`实现更快。

#### 设置Heap_4使用的数组的起始地址

这个部分包含高级信息。仅仅要使用Heap_4，不必阅读或理解这个部分。

有时候，应用程序编写者需要将heap_4使用的数组放置在特定的内存地址。例如，FreeRTOS任务使用的堆栈是从堆中分配的，因此可能需要确保堆位于快速的内部内存中，而不是慢速的外部内存。

默认情况下，heap_4使用的数组在heap_4.c源文件内声明，其起始地址由链接器自动设置。但是，如果在FreeRTOSConfig.h中将`configAPPLICATION_ALLOCATED_HEAP`编译时配置常量设置为1，则该数组必须由使用FreeRTOS的应用程序声明。如果数组作为应用程序的一部分声明，那么应用程序的编写者可以设置其起始地址。

如果在FreeRTOSConfig.h中将`configAPPLICATION_ALLOCATED_HEAP`设置为1，则必须在应用程序的一个源文件中声明一个名为ucHeap的uint8_t数组，其尺寸由`configTOTAL_HEAP_SIZE`设置确定。

将变量放置在特定内存地址的语法取决于使用的编译器，因此请参考您的编译器文档。以下是两个编译器的示例：

- 清单 2显示了GCC编译器要求的语法，用于声明数组，并将数组放置在名为.my_heap的内存段中。
- 清单 3显示了IAR编译器要求的语法，用于声明数组，并将数组放置在绝对内存地址0x20000000处。

___
```c
uint8_t ucHeap[ configTOTAL_HEAP_SIZE ] __attribute__ ( ( section( ".my_heap" ) ) );
```
---
<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单2. 使用GCC语法来声明将由heap_4使用的数组，并将数组放置在名为.my_heap的内存段中。</div> </center>

___
```c
uint8_t ucHeap[ configTOTAL_HEAP_SIZE ] @ 0x20000000;
```
---
<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单3. 使用IAR语法声明将由heap_4使用的数组，并将数组放置在绝对地址0x20000000处。</div> </center>

#### Heap_5

heap_5用于分配和释放内存的算法与heap_4相同。与heap_4不同的是，heap_5不受限于从单个静态声明的数组分配内存；heap_5可以从多个不同且分隔的内存空间中分配内存。当FreeRTOS运行的系统提供的RAM在系统内存映射中不呈现为单一的连续（无间隔）块时，heap_5非常有用。

这意味着heap_5可以从多个不同的内存区域分配内存，这些内存区域可以在物理地址上分散，而不是形成一个连续的内存块。通过使用heap_5，FreeRTOS能够有效地管理来自这些分散的内存区域的内存，从而提供了更大的灵活性和可用性。这对于在资源有限的嵌入式系统上运行FreeRTOS的应用程序非常有帮助。

截止到目前为止，heap_5是唯一一个需要在调用`pvPortMalloc()`之前明确初始化的提供的内存分配方案。要初始化heap_5，需要使用`vPortDefineHeapRegions()` API函数。当使用heap_5时，必须在创建任何内核对象（任务、队列、信号量等）之前调用`vPortDefineHeapRegions()`。这确保了heap_5能够知道可用的内存区域，并能够在这些区域中分配内存。

初始化heap_5通常包括指定一组内存区域，从这些区域中可以分配内存。这些内存区域可以来自不同的物理地址或内存设备，以便提供更大的灵活性。然后，一旦heap_5被初始化，您可以开始创建任务、队列和其他内核对象，并使用`pvPortMalloc()`来分配内存。

这种初始化过程确保了内存管理器知道可用的内存资源以及如何使用它们。这对于在FreeRTOS中使用heap_5非常重要。

#### `vPortDefineHeapRegions()` API函数

`vPortDefineHeapRegions()` 用于指定构成 heap_5使用的总内存的每个独立内存区域的起始地址和大小。

---

```C
void vPortDefineHeapRegions( const HeapRegion_t * const pxHeapRegions );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单4.vPortDefineHeapRegions() API函数原型 。</div> </center>

每个独立的内存区域都由类型为 HeapRegion_t 的结构体描述。所有可用内存区域的描述被传递到`vPortDefineHeapRegions()` 中，作为 HeapRegion_t结构体的数组。

---

```C
typedef struct HeapRegion
{
 /* The start address of a block of memory that will be part of the heap.*/
 uint8_t *pucStartAddress;
 /* The size of the block of memory in bytes. */
 size_t xSizeInBytes;
} HeapRegion_t;
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单5.HeapRegion_t 结构体 。</div> </center>

|参数名/ 返回值|描述|
|--|--|
|pxHeapRegions|一个指向 HeapRegion_t 结构体数组的起始地址的指针。数组中的每个结构体描述了在使用 heap_5 时将成为堆的一部分的内存区域的起始地址和长度。|
||数组中的 HeapRegion_t 结构体必须按照起始地址排序；描述具有最低起始地址的内存区域的 HeapRegion_t 结构体必须是数组中的第一个结构体，而描述具有最高起始地址的内存区域的 HeapRegion_t 结构体必须是数组中的最后一个结构体。|
||数组的结尾由一个 HeapRegion_t 结构体标记，该结构体的 pucStartAddress 成员设置为 NULL。|

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 5. vPortDefineHeapRegions()参数 。</div> </center>

举个例子，考虑图 8 A 中显示的假想内存映射，其中包含三个独立的RAM块：RAM1、RAM2 和 RAM3。假设可执行代码被放置在只读内存中，这里没有显示。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure8.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图8. 内存映射。</div> </center>

清单 6 显示了一个 HeapRegion_t结构体数组，这些结构体共同完整描述了三个 RAM 块。

---

```c
/* Define the start address and size of the three RAM regions. */
#define RAM1_START_ADDRESS ( ( uint8_t * ) 0x00010000 )
#define RAM1_SIZE ( 65 * 1024 )
#define RAM2_START_ADDRESS ( ( uint8_t * ) 0x00020000 )
#define RAM2_SIZE ( 32 * 1024 )
#define RAM3_START_ADDRESS ( ( uint8_t * ) 0x00030000 )
#define RAM3_SIZE ( 32 * 1024 )
/* Create an array of HeapRegion_t definitions, with an index for each of the three
RAM regions, and terminating the array with a NULL address. The HeapRegion_t
structures must appear in start address order, with the structure that contains the
lowest start address appearing first. */
const HeapRegion_t xHeapRegions[] =
{
 { RAM1_START_ADDRESS, RAM1_SIZE },
 { RAM2_START_ADDRESS, RAM2_SIZE },
 { RAM3_START_ADDRESS, RAM3_SIZE },
 { NULL, 0 } /* Marks the end of the array. */
};
int main( void )
{
    /* Initialize heap_5. */
    vPortDefineHeapRegions( xHeapRegions );
    /* Add application code here. */
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单6. HeapRegion_t结构体数组，共同完整描述 RAM 的 3 个区域 。</div> </center>

虽然清单 6正确描述了RAM，但它没有演示一个可用的示例，因为它将所有的RAM分配给了堆，没有留下RAM供其他变量使用。 当项目被构建时，构建过程的链接阶段会为每个变量分配一个RAM地址。链接器可用于链接的RAM通常由链接器配置文件来描述，例如链接脚本。在图 8 B中，假设链接脚本包含了关于RAM1的信息，但没有包含关于RAM2或RAM3的信息。因此，链接器已经将变量放置在RAM1中，只留下RAM1地址0x0001nnnn之上的部分可供heap_5使用。实际的0x0001nnnn的值将取决于被链接应用程序中所有变量的组合大小。链接器将RAM2和RAM3的整个部分都留下未使用，使RAM2和RAM3的整个部分都可供heap_5使用。

如果使用清单 6中显示的代码，分配给heap_5的RAM将在地址0x0001nnnn以下，与保存变量的RAM重叠。为了避免这种情况，xHeapRegions[]数组中的第一个HeapRegion_t结构体可以使用0x0001nnnn作为起始地址，而不是0x00010000作为起始地址。然而，这不是一个推荐的解决方案，因为：

1. 起始地址可能不容易确定。
2. 链接器使用的RAM量在将来的构建中可能会发生变化，需要更新HeapRegion_t结构体中使用的起始地址。
3. 如果链接器使用的RAM和heap_5使用的RAM重叠，构建工具无法进行区分，因此无法警告应用程序编写者。

清单 7演示了一个更方便和易于维护的示例。它声明了一个名为ucHeap的数组。ucHeap是一个普通变量，因此它成为了链接器分配给RAM1的数据的一部分。xHeapRegions数组中的第一个HeapRegion_t结构体描述了ucHeap的起始地址和大小，因此ucHeap成为了由heap_5管理的内存的一部分。ucHeap的大小可以增加，直到链接器使用的RAM占用RAM1的全部内容，如图 8 C所示。

---

```c
/* Define the start address and size of the two RAM regions not used by the
linker. */
#define RAM2_START_ADDRESS ( ( uint8_t * ) 0x00020000 )
#define RAM2_SIZE ( 32 * 1024 )
#define RAM3_START_ADDRESS ( ( uint8_t * ) 0x00030000 )
#define RAM3_SIZE ( 32 * 1024 )
/* Declare an array that will be part of the heap used by heap_5. The array will be
placed in RAM1 by the linker. */
#define RAM1_HEAP_SIZE ( 30 * 1024 )
static uint8_t ucHeap[ RAM1_HEAP_SIZE ];
/* Create an array of HeapRegion_t definitions. Whereas in Listing 6 the first entry
described all of RAM1, so heap_5 will have used all of RAM1, this time the first
entry only describes the ucHeap array, so heap_5 will only use the part of RAM1 that
contains the ucHeap array. The HeapRegion_t structures must still appear in start
address order, with the structure that contains the lowest start address appearing
first. */
const HeapRegion_t xHeapRegions[] =
{
 { ucHeap, RAM1_HEAP_SIZE },
 { RAM2_START_ADDRESS, RAM2_SIZE },
 { RAM3_START_ADDRESS, RAM3_SIZE },
 { NULL, 0 } /* Marks the end of the array. */
};
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单7. 一个HeapRegion_t结构数组，描述了RAM2的全部内容，RAM3的全部内容，但只描述了RAM1的一部分。</div> </center>

清单 7中演示的技巧的优点包括：

1. 不需要使用硬编码的起始地址。
2. HeapRegion_t结构体中使用的地址将由链接器自动设置，因此即使在将来的构建中链接器使用的RAM数量发生变化，地址仍将始终正确。
3. 不可能使分配给heap_5的RAM与链接器放入RAM1的数据重叠。
4. 如果ucHeap太大，应用程序将无法链接。

### 与堆相关的实用函数

#### `xPortGetFreeHeapSize()` API 函数

`xPortGetFreeHeapSize()` API函数在调用时返回堆中的空闲字节数。它可以用于优化堆的大小。例如，如果在创建所有内核对象后`xPortGetFreeHeapSize()`返回2000，那么`configTOTAL_HEAP_SIZE`的值可以减少2000。 

在使用heap_3时，不可用`xPortGetFreeHeapSize()`函数。

---

```c
size_t xPortGetFreeHeapSize( void );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单8. xPortGetFreeHeapSize() API函数原型。</div> </center>

| 参数名/ 返回值 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| 返回值  | 在调用xPortGetFreeHeapSize()函数时，堆中剩余未分配的字节数。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 6. xPortGetFreeHeapSize()参数 。</div> </center>

#### `xPortGetMinimumEverFreeHeapSize() `API 函数

`xPortGetMinimumEverFreeHeapSize()` API函数返回自FreeRTOS应用程序开始执行以来，堆中曾经存在的未分配字节数的最小值。 `xPortGetMinimumEverFreeHeapSize()`返回的值表示应用程序在何时曾经接近耗尽堆空间。例如，如果`xPortGetMinimumEverFreeHeapSize()`返回200，那么在应用程序开始执行后的某个时刻，它曾经在距离堆空间耗尽还有200字节的情况下运行。

`xPortGetMinimumEverFreeHeapSize()`仅在使用heap_4或heap_5时可用。

---

```c
size_t xPortGetMinimumEverFreeHeapSize( void );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单9. xPortGetMinimumEverFreeHeapSize() API函数原型。</div> </center>

| 参数名/ 返回值 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| 返回值  | 自FreeRTOS应用程序开始执行以来，在堆中曾经存在的未分配字节数的最小值。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 7. xPortGetMinimumEverFreeHeapSize()参数 。</div> </center>

#### 分配内存失败钩子函数

`pvPortMalloc() `可以直接从应用程序代码中调用。它也在FreeRTOS源文件中的每次创建内核对象时调用。内核对象的示例包括任务、队列、信号量和事件组，这些都在本书的后续章节中描述。

就像标准库的`malloc()`函数一样，如果`pvPortMalloc()`无法返回所请求大小的内存块，因为所请求大小的内存块不存在，它将返回NULL。如果应用程序编写者正在创建一个内核对象导致`pvPortMalloc()`被执行，并且`pvPortMalloc()`的调用返回NULL，那么内核对象将不会被创建。

所有示例堆分配方案都可以配置为在`pvPortMalloc()`返回NULL时调用一个钩子（或回调）函数。

如果在FreeRTOSConfig.h中将`configUSE_MALLOC_FAILED_HOOK`设置为1，那么应用程序必须提供一个malloc分配失败的钩子函数，其名称和原型如清单 10所示。该函数可以根据应用程序的需要以任何适当的方式实现。

---

```c
void vApplicationMallocFailedHook( void );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单10. 分配内存失败钩子函数名称和原型。</div> </center>

## 任务管理

### 章节介绍与范围

#### 范围

本章旨在让读者充分了解以下内容：

- FreeRTOS如何分配处理时间给应用程序中的每个任务。

- FreeRTOS如何在任何给定时间选择要执行的任务。

- 每个任务的相对优先级如何影响系统行为。

- 任务可以存在的状态。

读者还应该充分了解以下内容：

- 如何实现任务。

- 如何创建一个或多个任务实例。

- 如何使用任务参数。

- 如何更改已经创建的任务的优先级。

- 如何删除一个任务。

- 如何使用任务实现周期性处理（软件定时器将在后面的章节讨论）。

- 空闲任务将何时执行以及如何使用它。 

本章中提出的概念对于理解如何使用FreeRTOS以及FreeRTOS应用程序的行为非常重要。因此，这是本书中最详细的章节。

### 任务函数

任务以C函数的形式实现。它们唯一特殊的地方在于它们的原型，必须返回void并接受一个void指针参数。原型如清单 11所示。

---

```c
void ATaskFunction( void *pvParameters );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单11.  任务函数原型。</div> </center>

每个任务本质上都是一个小型程序。它具有一个入口，通常在一个无限循环内永远运行，并且不会退出。典型任务的结构如清单 12所示。FreeRTOS任务绝不允许以任何形式从它们的实现函数中返回——它们不能包含“return”语句，并且不允许执行超出函数的末尾。如果不再需要一个任务，应该显式删除它。这也在清单 12中演示了出来。 单个任务函数定义可以用于创建任意数量的任务——每个创建的任务都是一个独立的执行实例，具有自己的堆栈以及在任务内部定义的任何自动（堆栈）变量的副本。

---

```c
void ATaskFunction( void *pvParameters )
{
    /* Variables can be declared just as per a normal function. Each instance of a task
    created using this example function will have its own copy of the lVariableExample
    variable. This would not be true if the variable was declared static – in which case
    only one copy of the variable would exist, and this copy would be shared by each
    created instance of the task. (The prefixes added to variable names are described in
    section 1.5, Data Types and Coding Style Guide.) */
    int32_t lVariableExample = 0;
    /* A task will normally be implemented as an infinite loop. */
    for( ;; )
    {
    /* The code to implement the task functionality will go here. */
    }
    /* Should the task implementation ever break out of the above loop, then the task
    must be deleted before reaching the end of its implementing function. The NULL
    parameter passed to the vTaskDelete() API function indicates that the task to be
    deleted is the calling (this) task. The convention used to name API functions is
    described in section 0, Projects that use a FreeRTOS version older than V9.0.0
    must build one of the heap_n.c files. From FreeRTOS V9.0.0 a heap_n.c file is only
    required if configSUPPORT_DYNAMIC_ALLOCATION is set to 1 in FreeRTOSConfig.h or if
    configSUPPORT_DYNAMIC_ALLOCATION is left undefined. Refer to Chapter 2, Heap Memory
    Management, for more information.
    Data Types and Coding Style Guide. */
    vTaskDelete( NULL );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单12.  典型任务函数的结构。</div> </center>

### 顶层任务状态

一个应用程序可以包含多个任务。如果运行应用程序的处理器只有一个核心，那么在任何给定的时间只能执行一个任务。这意味着一个任务可以处于两种状态之一，即运行状态和非运行状态。这个简化模型首先被考虑，但请记住这是一个过度简化的模型。在本章后面将展示，非运行状态实际上包含了许多子状态。 

当一个任务处于运行状态时，处理器正在执行该任务的代码。当一个任务处于非运行状态时，任务处于休眠状态，其状态已被保存，以便在调度程序决定它应该进入运行状态时，可以恢复执行。 当一个任务恢复执行时，它将从上次离开运行状态之前即将执行的指令开始执行。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure9.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图9. 顶层任务状态和状态转换。</div> </center>

从非运行状态到运行状态的任务转换被称为“切入”或“交换入”。相反，从运行状态到非运行状态的任务转换被称为“切出”或“交换出”。FreeRTOS调度程序是唯一可以切入和切出任务的实体。

### 创建任务

#### `xTaskCreate()`API 函数

任务是使用FreeRTOS的`xTaskCreate() `API函数创建的。 这可能是所有API函数中最复杂的一个，所以不幸的是它是第一个遇到的，但是任务必须首先掌握，因为它们是多任务系统中最基本的组件。随本书附带的所有示例都使用了`xTaskCreate()`函数，因此有很多示例可供参考。 

第1.5节《数据类型和编码风格指南》描述了使用的数据类型和命名约定。

---

```c
BaseType_t xTaskCreate( TaskFunction_t pvTaskCode,
                        const char * const pcName,
                        uint16_t usStackDepth,
                        void *pvParameters,
                        UBaseType_t uxPriority,
                        TaskHandle_t *pxCreatedTask );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单13.  xTaskCreate()API 函数原型。</div> </center>

| 参数名/ 返回值 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| pvTaskCode     | 任务只是不会退出的C函数，因此通常实现为一个无限循环。pvTaskCode参数只是指向实现任务的函数的指针（实际上就是函数的名称）。 |
| pcName         | 任务的描述性名称。FreeRTOS不会以任何方式使用它。它仅作为调试辅助工具包含在内。通过人类可读的名称来识别任务要比尝试通过其句柄来识别任务简单得多。 应用程序定义的常量configMAX_TASK_NAME_LEN定义了任务名称可以达到的最大长度，包括NULL终止符。提供比这个最大长度更长的字符串将导致字符串被静默截断。 |
| usStackDepth   | 每个任务都有自己独特的堆栈，在任务创建时由内核分配给任务。usStackDepth值告诉内核要创建多大的堆栈。 这个值指定了堆栈可以容纳的字数，而不是字节数。例如，如果堆栈宽度为32位，usStackDepth传入为100，那么将分配400字节的堆栈空间（100 * 4字节）。堆栈深度乘以堆栈宽度不能超过一个uint16_t类型的变量可以包含的最大值。 Idle任务使用的堆栈大小由应用程序定义的常量configMINIMAL_STACK_SIZE1定义。在所使用的处理器架构的FreeRTOS演示应用程序中，分配给这个常量的值是对任何任务的最低建议值。如果您的任务使用了大量堆栈空间，那么您必须分配一个更大的值。 没有一种简单的方法可以确定一个任务所需的堆栈空间。虽然可以进行计算，但大多数用户将简单地分配他们认为合理的值，然后使用FreeRTOS提供的功能来确保分配的空间确实足够，不会不必要地浪费RAM。第12.3节“堆栈溢出”中包含了如何查询任务实际使用的最大堆栈空间的信息。 |
| pvParameters   | 任务函数接受一个类型为`void`指针（`void*`）的参数。分配给`pvParameters`的值是传递给任务的值。本书中的一些示例演示了如何使用参数。 |
| uxPriority     | 定义任务将执行的优先级。优先级可以从0分配，这是最低优先级，到(configMAX_PRIORITIES - 1)，这是最高优先级。configMAX_PRIORITIES是一个用户定义的常量，在第3.5节中描述。 传递一个超过(configMAX_PRIORITIES - 1)的uxPriority值将导致任务分配的优先级被静默地限制为最大的合法值。 |
| pxCreatedTask  | pxCreatedTask作为传递给正在创建的任务的句柄。可以使用此句柄在API调用中引用任务，例如更改任务的优先级或删除任务。 如果您的应用程序不需要任务句柄，那么pxCreatedTask可以设置为NULL。 |
| 返回值  | 有两种可能的返回值：1. pdPASS 这表示任务已成功创建。2. pdFAIL 这表示任务未被创建，因为没有足够的堆内存可供FreeRTOS分配足够的RAM来存储任务数据结构和堆栈。第2章提供了关于堆内存管理的更多信息。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 8. xTaskCreate()参数和返回值 。</div> </center>

#### 示例1. 创建任务

这个示例演示了创建两个简单任务并启动这些任务执行所需的步骤。这些任务仅仅是周期性地打印一个字符串，使用一个简单的空循环来创建时间延迟。这两个任务以相同的优先级创建，除了它们要打印的字符串不同外，它们是相同的。请参考清单 14和清单 15，了解它们各自的实现。

---

```c
void vTask1( void *pvParameters )
{
    const char *pcTaskName = "Task 1 is running\r\n";
    volatile uint32_t ul; /* volatile to ensure ul is not optimized away. */
     /* As per most tasks, this task is implemented in an infinite loop. */
     for( ;; )
     {
         /* Print out the name of this task. */
         vPrintString( pcTaskName );
         /* Delay for a period. */
         for( ul = 0; ul < mainDELAY_LOOP_COUNT; ul++ )
         {
         /* This loop is just a very crude delay implementation. There is
         nothing to do in here. Later examples will replace this crude
         loop with a proper delay/sleep function. */
         }
     }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单14.  示例 1 中使用的第一个任务实现。</div> </center>

---

```c
void vTask2( void *pvParameters )
{
    const char *pcTaskName = "Task 2 is running\r\n";
    volatile uint32_t ul; /* volatile to ensure ul is not optimized away. */
    /* As per most tasks, this task is implemented in an infinite loop. */
    for( ;; )
    {
        /* Print out the name of this task. */
        vPrintString( pcTaskName );
        /* Delay for a period. */
        for( ul = 0; ul < mainDELAY_LOOP_COUNT; ul++ )
        {
        /* This loop is just a very crude delay implementation. There is
        nothing to do in here. Later examples will replace this crude
        loop with a proper delay/sleep function. */
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单15.  示例 1 中使用的第二个任务实现。</div> </center>

main() 函数在启动调度程序之前创建任务 — 请参阅清单 16 了解其内容执行。

---

```c
int main( void )
{
    /* Create one of the two tasks. Note that a real application should check
    the return value of the xTaskCreate() call to ensure the task was created
    successfully. */
    xTaskCreate( vTask1, /* Pointer to the function that implements the task. */
                "Task 1",/* Text name for the task. This is to facilitate
                debugging only. */
                1000, /* Stack depth - small microcontrollers will use much
                less stack than this. */
                NULL, /* This example does not use the task parameter. */
                1, /* This task will run at priority 1. */
                NULL ); /* This example does not use the task handle. */
    
    /* Create the other task in exactly the same way and at the same priority. */
    xTaskCreate( vTask2, "Task 2", 1000, NULL, 1, NULL );
    
    /* Start the scheduler so the tasks start executing. */
    vTaskStartScheduler();

    /* If all is well then main() will never reach here as the scheduler will
    now be running the tasks. If main() does reach here then it is likely that
    there was insufficient heap memory available for the idle task to be created.
    Chapter 2 provides more information on heap memory management. */
    for( ;; );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单16.  启动示例1中的任务。</div> </center>

执行示例会产生如图 10所示的输出[^5]。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure10.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图10. 执行示例1时产生的输出。</div> </center> 

[^5]:这个屏幕截图显示了每个任务在下一个任务执行之前精确地打印一次它的消息。这是由于使用了FreeRTOS Windows模拟器所产生的人为情景。Windows模拟器并不是真正的实时系统。另外，向Windows控制台写入数据需要相对长的时间，并导致一系列的Windows系统调用。在真正的嵌入式目标上执行相同的代码，使用快速且非阻塞的打印函数，可能会导致每个任务在切换出去以允许其他任务运行之前多次打印它的字符串。

图 10显示了两个任务看起来在同时执行；然而，由于这两个任务都在同一处理器核心上执行，这不可能是真的同时执行。实际上，这两个任务都在快速地进入和退出运行状态。这两个任务以相同的优先级运行，因此在同一处理器核心上共享时间。它们的实际执行模式如图 11所示。 

图 11底部的箭头显示了从时间t1开始经过的时间。着色的线条显示了每个时间点执行哪个任务，例如，在时间t1和时间t2之间，任务1正在执行。 在任何给定时间只能存在一个任务处于运行状态。因此，当一个任务进入运行状态时（任务被切入），另一个任务进入非运行状态（任务被切出）。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure11.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图11. 示例1中两个任务的实际执行模式。</div> </center> 

示例1在启动调度程序之前从main()函数中创建了这两个任务。也可以在一个任务内部创建另一个任务。例如，任务2可以从任务1内部创建，如清单 17所示。

---

```c
void vTask1( void *pvParameters )
{
    const char *pcTaskName = "Task 1 is running\r\n";
    volatile uint32_t ul; /* volatile to ensure ul is not optimized away. */
    /* If this task code is executing then the scheduler must already have
    been started. Create the other task before entering the infinite loop. */
    xTaskCreate( vTask2, "Task 2", 1000, NULL, 1, NULL );
    for( ;; )
    {
        /* Print out the name of this task. */
        vPrintString( pcTaskName );
        /* Delay for a period. */
        for( ul = 0; ul < mainDELAY_LOOP_COUNT; ul++ )
        {
        /* This loop is just a very crude delay implementation. There is
        nothing to do in here. Later examples will replace this crude
        loop with a proper delay/sleep function. */
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单17. 在调度程序启动后从一个任务内部创建另一个任务。</div> </center>

#### 示例2. 使用任务参数

在示例1中创建的两个任务几乎是相同的，它们之间唯一的区别是它们打印出的文本字符串。通过创建两个单一任务实例的方式，可以消除这种重复，然后可以使用任务参数将每个任务应该打印的字符串传递给它。

清单 18包含了示例2中使用的单一任务函数（vTaskFunction）的代码。这个单一函数替代了示例1中使用的两个任务函数（vTask1和vTask2）。请注意，任务参数被转换为char *以获取任务应该打印的字符串。

---

```c
void vTaskFunction( void *pvParameters )
{
    char *pcTaskName;
    volatile uint32_t ul; /* volatile to ensure ul is not optimized away. */
    /* The string to print out is passed in via the parameter. Cast this to a
    character pointer. */
    pcTaskName = ( char * ) pvParameters;
    /* As per most tasks, this task is implemented in an infinite loop. */
    for( ;; )
    {
        /* Print out the name of this task. */
        vPrintString( pcTaskName );
        /* Delay for a period. */
        for( ul = 0; ul < mainDELAY_LOOP_COUNT; ul++ )
        {
        /* This loop is just a very crude delay implementation. There is
        nothing to do in here. Later exercises will replace this crude
        loop with a proper delay/sleep function. */
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单18. 在示例2中用于创建两个任务的单一任务函数。</div> </center>

尽管现在只有一个任务实现（vTaskFunction），仍然可以创建多个定义的任务实例。每个创建的实例将在FreeRTOS调度程序的控制下独立执行。 

清单 19显示了如何使用`xTaskCreate()`函数的`pvParameters`参数将文本字符串传递到任务中。

---

```c
/* Define the strings that will be passed in as the task parameters. These are
defined const and not on the stack to ensure they remain valid when the tasks are
executing. */
static const char *pcTextForTask1 = "Task 1 is running\r\n";
static const char *pcTextForTask2 = "Task 2 is running\r\n";
int main( void )
{
    /* Create one of the two tasks. */
    xTaskCreate( vTaskFunction, /* Pointer to the function that
                 implements the task. */
                 "Task 1", /* Text name for the task. This is to
                 facilitate debugging only. */
                 1000, /* Stack depth - small microcontrollers
                 will use much less stack than this. */
                 (void*)pcTextForTask1, /* Pass the text to be printed into the
                 task using the task parameter. */
                 1, /* This task will run at priority 1. */
                 NULL ); /* The task handle is not used in this
    example. */
    /* Create the other task in exactly the same way. Note this time that multiple
    tasks are being created from the SAME task implementation (vTaskFunction). Only
    the value passed in the parameter is different. Two instances of the same
    task are being created. */
    xTaskCreate( vTaskFunction, "Task 2", 1000, (void*)pcTextForTask2, 1, NULL );
    /* Start the scheduler so the tasks start executing. */
    vTaskStartScheduler();
    /* If all is well then main() will never reach here as the scheduler will
    now be running the tasks. If main() does reach here then it is likely that
    there was insufficient heap memory available for the idle task to be created.
    Chapter 2 provides more information on heap memory management. */
    for( ;; );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单19. 示例2中的main()函数。</div> </center>

示例2的输出与示例1中图 10所示的输出完全相同。

### 任务优先级

`xTaskCreate()` API函数的`uxPriority`参数分配了一个初始优先级给正在创建的任务。在调度程序启动后，可以使用`vTaskPrioritySet() `API函数来更改任务的优先级。 

可用的最大优先级数量由FreeRTOSConfig.h中的应用程序定义的`configMAX_PRIORITIES`编译时配置常量设置。低数值优先级表示低优先级任务，优先级0是可能的最低优先级。因此，可用优先级的范围是0到(configMAX_PRIORITIES - 1)。任何数量的任务都可以共享相同的优先级，确保最大的设计灵活性。 FreeRTOS调度程序可以使用两种方法来决定哪个任务将处于运行状态

`configMAX_PRIORITIES`可以设置的最大值取决于所使用的方法：

1. 通用方法 

   通用方法是用C语言实现的，可以与所有FreeRTOS架构端口一起使用。 

   使用通用方法时，FreeRTOS不会限制configMAX_PRIORITIES可以设置的最大值。然而，始终建议将configMAX_PRIORITIES值保持在最低必要值，因为它的值越高，将消耗的RAM越多，最坏情况下的执行时间将变长。

   如果在FreeRTOSConfig.h中将`configUSE_PORT_OPTIMISED_TASK_SELECTION`设置为0，或者如果未定义`configUSE_PORT_OPTIMISED_TASK_SELECTION`，或者如果通用方法是正在使用的FreeRTOS端口唯一提供的方法，那么将使用通用方法。

2. 架构优化方法 

   架构优化方法使用少量的汇编代码，比通用方法更快。`configMAX_PRIORITIES`的设置不会影响最坏情况下的执行时间。如果使用架构优化方法，那么`configMAX_PRIORITIES`不能大于32。与通用方法一样，建议将`configMAX_PRIORITIES`保持在最低必要值，因为它的值越高，将消耗的RAM越多。

   如果在FreeRTOSConfig.h中将`configUSE_PORT_OPTIMISED_TASK_SELECTION`设置为1，则将使用架构优化方法。

   并非所有FreeRTOS端口都提供架构优化方法。

FreeRTOS调度程序将始终确保能够运行的最高优先级任务是被选择进入Running状态的任务。如果有多个具有相同优先级的任务可以运行，调度程序将依次将每个任务切换到Running状态，然后再切出。

### 时间测量和滴答中断

第3.12节 调度算法，描述了一个可选的功能称为“时间片轮转”。迄今为止所呈现的示例中均使用了时间片轮转，这是从它们产生的输出中观察到的行为。在示例中，两个任务都以相同的优先级创建，两个任务始终可以运行。因此，每个任务都在一个“时间片”内执行，从一个时间片的开始进入Running状态，然后在时间片结束时退出Running状态。在图 11中，t1和t2之间的时间等于一个时间片。

为了能够选择下一个要运行的任务，调度程序本身必须在每个时间片的末尾执行[^6]。一个周期性的中断，称为“滴答中断”（tick interrupt），用于此目的。时间片的长度实际上由滴答中断频率来设置，该频率取决于FreeRTOSConfig.h中的用户定义的`configTICK_RATE_HZ`编译时配置常量。例如，如果`configTICK_RATE_HZ`设置为100（Hz），那么时间片将为10毫秒。两个滴答中断之间的时间称为“滴答周期”。一个时间片等于一个滴答周期。

图 11可以扩展以显示调度程序自身在执行序列中的执行情况。这在图 12中展现，顶部线显示了调度程序何时在执行，细箭头显示了从一个任务到滴答中断的执行顺序，然后从滴答中断返回到不同任务。

`configTICK_RATE_HZ`的最佳值取决于正在开发的应用程序，尽管典型值为100。

[^6]:需要注意的是，在时间片的结束并不是调度程序唯一选择新任务运行的时机；正如本书中将要演示的，调度程序还会在当前正在执行的任务进入阻塞状态后立即选择一个新任务运行，或者当中断将一个更高优先级的任务移到就绪状态时。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure12.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图12. 扩展的执行序列以显示滴答中断的执行。</div> </center> 

FreeRTOS API调用总是以滴答周期的倍数来指定时间，通常简称为“滴答”。`pdMS_TO_TICKS()`宏将以毫秒为单位指定的时间转换为以滴答为单位的时间。可用的分辨率取决于定义的滴答频率，如果滴答频率高于1KHz（如果configTICK_RATE_HZ大于1000），则不能使用pdMS_TO_TICKS()。清单 20展示了如何使用`pdMS_TO_TICKS()`将以200毫秒指定的时间转换为以滴答为单位的等效时间。

---

```c
/* pdMS_TO_TICKS() takes a time in milliseconds as its only parameter, and evaluates
to the equivalent time in tick periods. This example shows xTimeInTicks being set to
the number of tick periods that are equivalent to 200 milliseconds. */
TickType_t xTimeInTicks = pdMS_TO_TICKS( 200 );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单20. 使用pdMS_TO_TICKS()宏将200毫秒转换为以滴答周期表示的等效时间。</div> </center>

注意：不建议在应用程序中直接以滴答为单位指定时间，而是应该使用pdMS_TO_TICKS()宏以毫秒为单位指定时间。这样做可以确保在应用程序中指定的时间不会因为更改滴答频率而改变。

'滴答计数'值是自从调度程序启动以来发生的滴答中断的总数，假设滴答计数没有溢出。用户应用程序在指定延迟周期时不必考虑溢出，因为时间一致性是由FreeRTOS内部进行管理的。

第3.12节 调度算法，描述了影响调度程序何时选择新任务运行以及何时执行滴答中断的配置常量。这些配置常量可以调整FreeRTOS的行为，以满足特定应用程序的需求。例如，您可以通过更改配置常量来调整任务的优先级、滴答中断的频率等，以适应不同的应用场景。配置常量允许您自定义FreeRTOS的行为，以满足特定的性能和实时要求。

#### 示例3. 优先级实验

调度程序将始终确保能够运行的最高优先级任务被选中进入Running状态。在我们迄今为止的示例中，两个任务以相同的优先级创建，因此它们轮流进入和退出Running状态。这个示例探讨了在改变示例 2中创建的两个任务之一的优先级时会发生什么情况。这次，第一个任务将以优先级1创建，第二个任务将以优先级2创建。创建任务的代码如清单 21所示。实现这两个任务的单个函数并未更改；它仍然只是定期打印一个字符串，使用空循环创建延迟。

---

```c
/* Define the strings that will be passed in as the task parameters. These are defined const and not on the stack to ensure they remain valid when the tasks are
executing. */
static const char *pcTextForTask1 = "Task 1 is running\r\n";
static const char *pcTextForTask2 = "Task 2 is running\r\n";
int main( void )
{
    /* Create the first task at priority 1. The priority is the second to last
    parameter. */
    xTaskCreate( vTaskFunction, "Task 1", 1000, (void*)pcTextForTask1, 1, NULL );
    /* Create the second task at priority 2, which is higher than a priority of 1.
    The priority is the second to last parameter. */
    xTaskCreate( vTaskFunction, "Task 2", 1000, (void*)pcTextForTask2, 2, NULL );
    /* Start the scheduler so the tasks start executing. */
    vTaskStartScheduler();
    /* Will not reach here. */
    return 0;
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单21. 创建两个不同优先级的任务。</div> </center>

示例3产生的输出如图 13所示。

调度程序将始终选择能够运行的最高优先级任务。Task 2的优先级高于Task 1，并且始终能够运行；因此，只有Task 2会进入Running状态。由于Task 1从未进入Running状态，因此它从不打印出其字符串。Task 1称为被Task 2“挤占”了处理时间。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure13.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图13. 在不同的优先级下运行两个任务。</div> </center> 

Task 2总是能够运行，因为它从不需要等待任何东西——它要么在一个空循环中循环，要么在终端上打印。 

图 14显示了示例3的执行顺序。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure14.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图14. 当一个任务的优先级高于另一个任务时的执行模式。</div> </center> 

### 扩充"非运行"态

到目前为止，创建的任务一直有处理任务，从未等待任何东西——因为它们永远不需要等待，所以它们总是能够进入Running状态。这种“连续处理”的任务用处有限，因为它们只能以最低的优先级创建。如果它们以其他任何优先级运行，它们将抢占更低优先级的任务使其根本无法运行。

为了使任务有效，它们必须被重写为事件驱动。事件驱动任务仅在触发它的事件发生后才有工作（处理任务）要执行，并且在该事件发生之前不能进入Running状态。调度程序总是选择能够运行的最高优先级任务。高优先级任务无法运行意味着调度程序无法选择它们，而必须选择能够运行的较低优先级任务。因此，使用事件驱动任务意味着可以以不同的优先级创建任务，而不会让最高优先级的任务挤占所有较低优先级任务的处理时间。

#### 阻塞状态

等待事件的任务被称为处于“阻塞”状态，这是“未运行”状态的一个子状态。 任务可以进入阻塞状态等待两种不同类型的事件：

1. 时间相关的事件——事件可以是延迟时间到期或达到绝对时间。例如，任务可以进入阻塞状态等待10毫秒的时间过去。
2. 同步事件——事件源于另一个任务或中断。例如，任务可以进入阻塞状态等待队列中的数据到达。同步事件涵盖了广泛的事件类型。 

FreeRTOS队列、二值信号量、计数信号量、互斥量、递归互斥量、事件组和直接任务通知都可以用于创建同步事件。所有这些特性都将在本书的后续章节中介绍。

任务可以在带有超时的同步事件上阻塞，甚至同时阻塞在两种类型的事件上。例如，任务可以选择等待最多10毫秒直至队列数据到达。如果数据在10毫秒内到达，或者在10毫秒内没有数据到达，任务都将离开阻塞状态。

#### 挂起状态

挂起状态也是未运行状态的子状态。处于挂起状态的任务对调度程序不可用。进入挂起状态的唯一方式是通过调用`vTaskSuspend() `API函数，而唯一的退出方式是通过调用`vTaskResume()`或`xTaskResumeFromISR()` API函数。大多数应用程序不使用挂起状态。 

#### 就绪状态

处于“未运行”状态但不是阻塞或挂起状态的任务被称为处于就绪状态。它们能够运行，并且“准备好”运行，但目前不处于运行状态。 

#### 完成状态转换图

图 15扩展了以前过于简化的状态图，包括本节描述的所有“未运行”子状态。到目前为止，在示例中创建的任务没有使用阻塞或挂起状态；它们只在“就绪”状态和“运行”状态之间转换——在图 15中以粗线突出显示。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure15.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图15. 完整的任务状态机。</div> </center> 

#### 示例4. 使用阻塞状态实现延迟

到目前为止，在所提供的示例中，所有创建的任务都是“周期性的”——它们延迟一段时间，然后打印出它们的字符串，然后再次延迟，如此反复。延迟是使用非常粗糙的空循环生成的——任务实际上是通过轮询递增的循环计数器，直到达到固定值。示例3清楚地展示了这种方法的劣势。较高优先级的任务在执行空循环时保持在运行状态，而较低优先级的任务没有任何处理时间。

任何形式的轮询都有几个缺点，其中最重要的是效率低下。在轮询期间，任务实际上没有任何工作要做，但它仍然使用了最大的处理时间，因此浪费了处理器周期。示例4通过将空循环替换为调用`vTaskDelay()` API函数来纠正了这种行为，其原型如清单 22所示。新的任务定义如清单 23所示。请注意，只有在FreeRTOSConfig.h中将`INCLUDE_vTaskDelay`设置为1时，才可以使用`vTaskDelay()` API函数。

`vTaskDelay()`将调用任务放入阻塞状态，以固定数量的时钟中断来计算。任务在阻塞状态时不使用任何处理时间，因此任务只在实际需要进行工作时使用处理时间。

---

```c
void vTaskDelay( TickType_t xTicksToDelay );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单22.  vTaskDelay()API 函数原型。</div> </center>

| 参数名/ 返回值 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| xTicksToDelay | 调用任务在被转换回就绪状态之前将保持在阻塞状态的滴答中断数目。 |
|          | 例如，如果一个任务在滴答计数为10,000时调用vTaskDelay(100)，那么它将立即进入阻塞状态，并在滴答计数达到10,100时退出阻塞状态。 |
|    | 宏pdMS_TO_TICKS()可用于将以毫秒为单位指定的时间转换为以滴答为单位指定的时间。例如，调用vTaskDelay(pdMS_TO_TICKS(100))将导致调用任务在阻塞状态中保持100毫秒。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 9. vTaskDelay()参数。</div> </center>

---

```c
void vTaskFunction( void *pvParameters )
{
    char *pcTaskName;
    const TickType_t xDelay250ms = pdMS_TO_TICKS( 250 );
    /* The string to print out is passed in via the parameter. Cast this to a
    character pointer. */
    pcTaskName = ( char * ) pvParameters;
    /* As per most tasks, this task is implemented in an infinite loop. */
    for( ;; )
    {
        /* Print out the name of this task. */
        vPrintString( pcTaskName );
        /* Delay for a period. This time a call to vTaskDelay() is used which places
        the task into the Blocked state until the delay period has expired. The
        parameter takes a time specified in ‘ticks’, and the pdMS_TO_TICKS() macro
        is used (where the xDelay250ms constant is declared) to convert 250
        milliseconds into an equivalent time in ticks. */
        vTaskDelay( xDelay250ms );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单23. 将空循环延迟替换为调用vTaskDelay()后，示例任务的源代码。</div> </center>

尽管两个任务仍然以不同的优先级创建，但现在两者都将运行。示例4的输出如图 16所示，证实了预期的行为。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure16.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图16. 执行示例4时产生的输出。</div> </center> 

图 17中显示的执行顺序解释了为什么尽管它们在不同的优先级下创建，但这两个任务都会运行。出于简化的原因，省略了调度程序本身的执行。

空闲任务在启动调度程序时会自动创建，以确保始终至少有一个能够运行的任务（至少一个就绪状态的任务）。第3.8节“空闲任务和空闲任务挂钩”详细描述了空闲任务。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure17.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图17. 任务使用vTaskDelay()替代空循环时的执行顺序。</div> </center> 

两个任务的实现发生了变化，但它们的功能保持不变。将图 17与图 12进行比较清楚地表明，这种功能是以更加高效的方式实现的。 

图 12显示了任务使用空循环创建延迟时的执行模式，它们始终能够运行，结果占用了它们之间的所有的处理器时间。图 17显示了任务在其整个延迟周期内进入阻塞状态的执行模式，因此仅在实际需要执行工作（在本例中仅为打印消息）时才使用处理器时间，并因此仅使用了可用处理时间的一小部分。 

在图 17的情况下，每当任务离开阻塞状态时，它们在重新进入阻塞状态之前执行不到一个时钟周期的时间。大多数情况下，没有应用程序任务能够运行（就绪状态中没有应用程序任务），因此无法选择任何应用程序任务进入运行状态。在这种情况下，空闲任务将运行。分配给空闲任务的处理时间量是系统中的空余处理能力的度量。使用RTOS可以通过完全基于事件的应用程序显著增加系统中的空余处理能力。 

图 18中的粗线显示了示例4中任务执行的转换，每个任务现在都通过阻塞状态进行转换，然后返回到就绪状态。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure18.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图18. 粗线表示示例4中任务执行的状态转换。</div> </center> 

#### `vTaskDelayUntil()` API 函数

与`vTaskDelay()`类似。正如刚才展示的，`vTaskDelay()`参数指定了任务在调用`vTaskDelay()`之后和再次从Blocked状态转换到Ready状态之间应发生的时钟中断次数。任务在Blocked状态中停留的时间由`vTaskDelay()`参数指定，但任务离开Blocked状态的时间相对于调用`vTaskDelay()`的时间。

与此不同的是，`vTaskDelayUntil()`的参数指定了调用任务应该从Blocked状态转换到Ready状态的确切时钟计数值。当需要固定的执行周期时（您希望任务以固定的频率定期执行），应使用`vTaskDelayUntil()` API函数，因为调用任务解除阻塞的时间是绝对的，而不是相对于函数调用的时间（与`vTaskDelay()`不同）。

---

```c
void vTaskDelayUntil( TickType_t * pxPreviousWakeTime, TickType_t xTimeIncrement );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单24.  vTaskDelayUntil()API 函数原型。</div> </center>

| 参数名/ 返回值     | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| pxPreviousWakeTime | 此参数的名称是基于vTaskDelayUntil()被用于实现以固定频率定期执行的任务的假设。在这种情况下，pxPreviousWakeTime保存了任务上次离开Blocked状态（被“唤醒”）的时间。这个时间被用作计算任务下次离开Blocked状态的时间的参考点。 |
|      | 由pxPreviousWakeTime指向的变量在vTaskDelayUntil()函数内部会自动更新；通常情况下，它不会被应用程序代码修改，但在第一次使用之前必须初始化为当前的时钟计数。清单 25演示了初始化的方法。 |
| xTimeIncrement     | 这个参数的名称也是基于vTaskDelayUntil()被用于实现一个定期以固定频率执行的任务的假设，频率由xTimeIncrement值设置。 |
|      | xTimeIncrement以“滴答”为单位指定。可以使用宏pdMS_TO_TICKS()将以毫秒为单位指定的时间转换为以滴答为单位指定的时间。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 10. vTaskDelayUntil()参数。</div> </center>

#### 示例 5. 将示例任务转换为使用 vTaskDelayUntil()

在示例 4 中创建的两个任务是周期性任务，但使用 vTaskDelay() 不能保证它们运行的频率是固定的，因为任务离开阻塞状态的时间是相对于它们调用 vTaskDelay() 的时间而言的。将这些任务转换为使用 vTaskDelayUntil() 而不是 vTaskDelay() 可以解决这个潜在问题。

---

```c
void vTaskFunction( void *pvParameters )
{
    char *pcTaskName;
    TickType_t xLastWakeTime;
    /* The string to print out is passed in via the parameter. Cast this to a
    character pointer. */
    pcTaskName = ( char * ) pvParameters;
    /* The xLastWakeTime variable needs to be initialized with the current tick
    count. Note that this is the only time the variable is written to explicitly.
    After this xLastWakeTime is automatically updated within vTaskDelayUntil(). */
    xLastWakeTime = xTaskGetTickCount();
    /* As per most tasks, this task is implemented in an infinite loop. */
    for( ;; )
    {
        /* Print out the name of this task. */
        vPrintString( pcTaskName );
        /* This task should execute every 250 milliseconds exactly. As per
        the vTaskDelay() function, time is measured in ticks, and the
        pdMS_TO_TICKS() macro is used to convert milliseconds into ticks.
        xLastWakeTime is automatically updated within vTaskDelayUntil(), so is not
        explicitly updated by the task. */
        vTaskDelayUntil( &xLastWakeTime, pdMS_TO_TICKS( 250 ) );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单25.  使用 vTaskDelayUntil() 实现示例任务。</div> </center>

示例5的输出与图 16中显示的示例4的输出完全相同。

#### 示例6. 合并阻塞和非阻塞任务

以前的示例分别研究了轮询任务和阻塞任务的行为。这个示例通过演示两种方案的结合来加强所述的预期系统行为，具体如下：

1. 创建两个优先级为1的任务。这些任务除了不断打印一个字符串之外什么都不做。 这些任务从不调用可能导致它们进入阻塞状态的API函数，因此始终处于就绪状态或运行状态。这种性质的任务称为“连续处理”任务，因为它们始终有工作要做（尽管在这种情况下，工作相当微不足道）。连续处理任务的源代码示例如下所示（见清单 26）。
2. 然后创建第三个任务，其优先级为2，因此高于其他两个任务的优先级。第三个任务同样只是定期打印出一个字符串，但这次它会在每次打印迭代之间使用vTaskDelayUntil() API函数将自己置于阻塞状态。 周期性任务的源代码示例如下所示（见清单 27）。

---

```c
void vContinuousProcessingTask( void *pvParameters )
{
    char *pcTaskName;
    /* The string to print out is passed in via the parameter. Cast this to a
    character pointer. */
    pcTaskName = ( char * ) pvParameters;
    /* As per most tasks, this task is implemented in an infinite loop. */
    for( ;; )
    {
        /* Print out the name of this task. This task just does this repeatedly
        without ever blocking or delaying. */
        vPrintString( pcTaskName );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单26.  在示例6中使用的连续处理任务。</div> </center>

---

```c
void vPeriodicTask( void *pvParameters )
{
    TickType_t xLastWakeTime;
    const TickType_t xDelay3ms = pdMS_TO_TICKS( 3 );
    /* The xLastWakeTime variable needs to be initialized with the current tick
    count. Note that this is the only time the variable is explicitly written to.
    After this xLastWakeTime is managed automatically by the vTaskDelayUntil()
    API function. */
    xLastWakeTime = xTaskGetTickCount();
    /* As per most tasks, this task is implemented in an infinite loop. */
    for( ;; )
    {
        /* Print out the name of this task. */
        vPrintString( "Periodic task is running\r\n" );
        /* The task should execute every 3 milliseconds exactly – see the
        declaration of xDelay3ms in this function. */
        vTaskDelayUntil( &xLastWakeTime, xDelay3ms );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单27.  在示例6中使用的周期性任务。</div> </center>

图 19展示了示例6产生的输出，图 20显示了观察到的行为，并进行了解释。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure19.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图19. 执行示例6时产生的输出。</div> </center>

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure20.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图20. 示例6的执行模式。</div> </center>

### 空闲任务和空闲任务钩子函数

在示例4中创建的任务大部分时间都处于阻塞状态。在这种状态下，它们无法运行，因此无法被调度器选择。

始终必须至少存在一个任务能够进入运行状态[^7]。为确保这一点，在调用`vTaskStartScheduler()`时，调度器会自动创建一个空闲任务。空闲任务几乎什么都不做，只是在一个循环中等待——因此，就像最初的示例中的任务一样，它始终可以运行。

空闲任务具有最低可能的优先级（优先级为零），以确保它不会阻止更高优先级的应用程序任务进入运行状态——尽管没有什么可以阻止应用程序设计者在空闲任务的优先级上创建任务，如果需要的话。在FreeRTOSConfig.h中的`configIDLE_SHOULD_YIELD`编译时配置常量可以用于防止空闲任务消耗本应分配给应用程序任务的处理时间。`configIDLE_SHOULD_YIELD`在第3.12节“调度算法”中有描述。

以最低优先级运行可以确保空闲任务在更高优先级任务进入准备状态时立即退出运行状态。这可以在图 17的时间tn中看到，在这个时刻，空闲任务立即被替换出去，以允许任务2在任务2离开阻塞状态的瞬间执行。任务2被称为抢占了空闲任务。抢占是自动发生的，而被抢占的任务并不知道这一情况。

注意：如果应用程序使用`vTaskDelete()` API函数，那么非常重要的是不要让空闲任务被处理时间所耗尽。这是因为空闲任务负责在任务被删除后清理内核资源。

[^7]:即使在使用FreeRTOS的特殊低功耗功能的情况下这仍然适用，此时FreeRTOS正在执行的微控制器将在应用程序创建的任何任务都无法执行时进入低功耗模式。

#### 空闲任务挂钩函数

可以通过使用空闲任务挂钩函数（或空闲回调函数）将特定于应用程序的功能直接添加到空闲任务中，该函数会在空闲任务循环的每次迭代中自动调用。空闲任务挂钩的常见用途包括：

- 执行低优先级、后台或连续处理功能。
- 测量空余的处理容量。 （空闲任务仅在所有更高优先级的应用程序任务没有工作可执行时才运行；因此，测量分配给空闲任务的处理时间量清晰地指示了有多少处理时间是空闲的。）
- 将处理器置于低功耗模式，提供一种在没有应用程序处理需求时轻松自动节省电力的方法（尽管使用本方法可实现的节电效果比第10章“低功耗支持”中描述的无节拍空闲模式所能实现的效果要少）。

#### 空闲任务挂钩函数的实现限制

空闲任务挂钩函数必须遵守以下规则。

1. 空闲任务挂钩函数绝不能尝试阻塞或挂起。 注意：以任何方式阻塞空闲任务可能导致没有任务可以进入运行状态的情况。

2. 如果应用程序使用vTaskDelete() API函数，则空闲任务挂钩必须始终在合理的时间内返回给其调用者。这是因为空闲任务负责在任务被删除后清理内核资源。如果空闲任务永久停留在空闲挂钩函数中，那么这个清理操作无法进行。 

空闲任务挂钩函数必须具有清单 28所示的名称和原型。

---

```c
void vApplicationIdleHook( void );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单28.  空闲任务挂钩函数名称和原型。</div> </center>

#### 示例7. 定义一个空闲任务挂钩函数

在示例4中使用阻塞的`vTaskDelay()` API调用创建了大量的空闲时间——即当Idle任务正在执行，因为两个应用任务都处于阻塞状态。例子7通过添加一个空闲挂钩函数来利用这个空闲时间，其源代码如清单 29所示。

---

```c
/* Declare a variable that will be incremented by the hook function. */
volatile uint32_t ulIdleCycleCount = 0UL;
/* Idle hook functions MUST be called vApplicationIdleHook(), take no parameters,
and return void. */
void vApplicationIdleHook( void )
{
    /* This hook function does nothing but increment a counter. */
    ulIdleCycleCount++;
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单29.  一个非常简单的空闲挂钩函数。</div> </center>

要使空闲挂钩函数被调用，必须在FreeRTOSConfig.h中将`configUSE_IDLE_HOOK`设置为1。

实现创建任务的函数稍作修改，以打印出ulIdleCycleCount的值，如清单 30所示。

---

```c
void vTaskFunction( void *pvParameters )
{
    char *pcTaskName;
    const TickType_t xDelay250ms = pdMS_TO_TICKS( 250 );
    /* The string to print out is passed in via the parameter. Cast this to a
    character pointer. */
    pcTaskName = ( char * ) pvParameters;
    /* As per most tasks, this task is implemented in an infinite loop. */
    for( ;; )
    {
        /* Print out the name of this task AND the number of times ulIdleCycleCount
        has been incremented. */
        vPrintStringAndNumber( pcTaskName, ulIdleCycleCount );
        /* Delay for a period of 250 milliseconds. */
        vTaskDelay( xDelay250ms );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单30.  现在，示例任务的源代码会打印出ulIdleCycleCount的值。</div> </center>

示例7产生的输出如图 21所示。它显示了在每次应用程序任务迭代之间，空闲任务挂钩函数被调用了大约400万次（迭代次数取决于执行演示的硬件速度）。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure21.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图21. 执行示例7时产生的输出。</div> </center>

### 更改任务的优先级

#### `vTaskPrioritySet()` API 函数

vTaskPrioritySet()API函数可以在调度器启动后用于更改任何任务的优先级。请注意，只有在FreeRTOSConfig.h中将`INCLUDE_vTaskPrioritySet`设置为1时，vTaskPrioritySet() API函数才可用。

---

```c
void vTaskPrioritySet( TaskHandle_t pxTask, UBaseType_t uxNewPriority );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单31.  vTaskPrioritySet() API 函数原型。</div> </center>

| 参数名/ 返回值 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| pxTask         | 要修改优先级的任务的句柄（被修改优先级的任务），可以查看xTaskCreate() API函数的pxCreatedTask参数以获取任务句柄的信息。 |
|                | 任务可以通过将有效的任务句柄位置替换为NULL来更改自己的优先级。 |
| uxNewPriority | 要将受影响的任务的优先级设置为的值。这个值会自动限制在(configMAX_PRIORITIES - 1)的最大可用优先级之内，其中configMAX_PRIORITIES是在FreeRTOSConfig.h头文件中设置的一个编译时常量。 |


<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 11. vTaskPrioritySet()参数。</div> </center>

#### uxTaskPriorityGet() API 函数

uxTaskPriorityGet() API函数可用于查询任务的优先级。请注意，只有当FreeRTOSConfig.h中的`INCLUDE_uxTaskPriorityGet`设置为1时，uxTaskPriorityGet() API函数才可用。

---

```c
UBaseType_t uxTaskPriorityGet( TaskHandle_t pxTask );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单32.  uxTaskPriorityGet() API 函数原型。</div> </center>

| 参数名/ 返回值 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| pxTask         | 要查询优先级的任务的句柄（受查询的任务），可以查看xTaskCreate() API函数的pxCreatedTask参数以获取任务句柄的信息。 |
|                | 任务可以通过将有效的任务句柄位置替换为NULL来查询自己的优先级。 |
| 返回值         | 当前分配给被查询任务的优先级。                               |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 12. uxTaskPriorityGet()参数和返回值。</div> </center>

#### 示例8. 改变任务优先级

调度器总是会选择最高就绪状态的任务进入运行状态。示例8通过使用vTaskPrioritySet() API函数相对于彼此改变两个任务的优先级来演示这一点。

示例8创建了两个具有不同优先级的任务。这两个任务都不会调用任何可能导致它们进入阻塞状态的API函数，因此它们始终处于就绪状态或运行状态。因此，具有最高相对优先级的任务将始终被调度器选中以进入运行状态。

示例8的行为如下：

1. 任务1（清单 33）以最高优先级创建，因此保证首先运行。任务1在将任务2（清单 34）的优先级提升到自己的优先级之上之前打印出一些字符串。
2. 一旦任务2具有了最高相对优先级，它就开始运行（进入运行状态）。在任何时刻只能有一个任务处于运行状态，因此当任务2处于运行状态时，任务1处于就绪状态。
3. 任务2在将其自己的优先级设置回低于任务1的优先级之前打印出一条消息。
4. 任务2将其优先级设置回去意味着任务1再次成为最高优先级任务，因此任务1重新进入运行状态，迫使任务2回到就绪状态。

---

```c
void vTask1( void *pvParameters )
{
    UBaseType_t uxPriority;
    /* This task will always run before Task 2 as it is created with the higher
    priority. Neither Task 1 nor Task 2 ever block so both will always be in
    either the Running or the Ready state.
    Query the priority at which this task is running - passing in NULL means
    "return the calling task’s priority". */
    uxPriority = uxTaskPriorityGet( NULL );
    for( ;; )
    {
        /* Print out the name of this task. */
        vPrintString( "Task 1 is running\r\n" );
        /* Setting the Task 2 priority above the Task 1 priority will cause
        Task 2 to immediately start running (as then Task 2 will have the higher
        priority of the two created tasks). Note the use of the handle to task
        2 (xTask2Handle) in the call to vTaskPrioritySet(). Listing 35 shows how
        the handle was obtained. */
        vPrintString( "About to raise the Task 2 priority\r\n" );
        vTaskPrioritySet( xTask2Handle, ( uxPriority + 1 ) );
        /* Task 1 will only run when it has a priority higher than Task 2.
        Therefore, for this task to reach this point, Task 2 must already have
        executed and set its priority back down to below the priority of this
        task. */
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单33. 示例8中任务1的实现。</div> </center>

---

```c
void vTask2( void *pvParameters )
{
    UBaseType_t uxPriority;
    /* Task 1 will always run before this task as Task 1 is created with the
    higher priority. Neither Task 1 nor Task 2 ever block so will always be
    in either the Running or the Ready state.
    Query the priority at which this task is running - passing in NULL means
    "return the calling task’s priority". */
    uxPriority = uxTaskPriorityGet( NULL );
    for( ;; )
    {
        /* For this task to reach this point Task 1 must have already run and
        set the priority of this task higher than its own.
        Print out the name of this task. */
        vPrintString( "Task 2 is running\r\n" );
        /* Set the priority of this task back down to its original value.
        Passing in NULL as the task handle means "change the priority of the
        calling task". Setting the priority below that of Task 1 will cause
        Task 1 to immediately start running again – pre-empting this task. */
        vPrintString( "About to lower the Task 2 priority\r\n" );
        vTaskPrioritySet( NULL, ( uxPriority - 2 ) );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单34. 示例8中任务2的实现。</div> </center>

每个任务都可以使用NULL来查询和设置自己的优先级，而无需使用有效的任务句柄。只有当一个任务希望引用除自身之外的其他任务时，才需要任务句柄，比如当任务1改变任务2的优先级时。为了允许任务1这样做，任务2的句柄在任务2创建时被获取并保存，就像在图示 35的注释中突出显示的那样。

---

```c
/* Declare a variable that is used to hold the handle of Task 2. */
TaskHandle_t xTask2Handle = NULL;
int main( void )
{
    /* Create the first task at priority 2. The task parameter is not used
    and set to NULL. The task handle is also not used so is also set to NULL. */
    xTaskCreate( vTask1, "Task 1", 1000, NULL, 2, NULL );
    /* The task is created at priority 2 ______^. */
    /* Create the second task at priority 1 - which is lower than the priority
    given to Task 1. Again the task parameter is not used so is set to NULL -
    BUT this time the task handle is required so the address of xTask2Handle
    is passed in the last parameter. */
    xTaskCreate( vTask2, "Task 2", 1000, NULL, 1, &xTask2Handle );
    /* The task handle is the last parameter _____^^^^^^^^^^^^^ */
    /* Start the scheduler so the tasks start executing. */
    vTaskStartScheduler();
    /* If all is well then main() will never reach here as the scheduler will
    now be running the tasks. If main() does reach here then it is likely there
    was insufficient heap memory available for the idle task to be created.
    Chapter 2 provides more information on heap memory management. */
    for( ;; );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单35. 示例8的main()函数的实现。</div> </center>

图 22展示了示例8任务执行的顺序，而图 23则显示了相应的输出。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure22.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图22. 运行示例8时，任务的执行顺序。</div> </center> 

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure23.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图23. 执行示例8时产生的输出。</div> </center> 

### 删除任务

#### `vTaskDelete()` API 函数

任务可以使用vTaskDelete() API函数来删除自己或其他任务。请注意，只有当在FreeRTOSConfig.h中将`INCLUDE_vTaskDelete`设置为1时，vTaskDelete() API函数才可用。

已删除的任务不再存在，无法再次进入运行状态。

空闲任务负责释放已删除的任务分配的内存。因此，使用vTaskDelete() API函数的应用程序不应该完全将所有处理时间耗尽在空闲任务上。

注意：只有由内核自身分配给任务的内存在任务被删除时会自动释放。任务实现的分配的任何内存或其他资源必须显式释放。

---

```c
void vTaskDelete( TaskHandle_t pxTaskToDelete );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单36. vTaskDelete() API 函数原型。</div> </center>

| 参数名/ 返回值 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| pxTaskToDelete | 要删除任务的句柄（被删除的任务）可以通过查看xTaskCreate() API函数的pxCreatedTask参数以获取有关获取任务句柄的信息。 |
|                | 任务可以通过将有效的任务句柄位置替换为NULL来删除自己。       |


<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 13. vTaskDelete()参数。</div> </center>

#### 示例9. 删除任务

这是一个非常简单的示例，行为如下：

1. Task 1由main()以优先级1创建。当它运行时，它创建了优先级为2的Task 2。此时，Task 2是最高优先级任务，因此它立即开始执行。main()的源代码如清单 37所示，Task 1的源代码如清单 38所示。
2. Task 2除了删除自己什么也不做。它可以通过将NULL传递给vTaskDelete()来删除自己，但为了演示目的，它使用了自己的任务句柄。Task 2的源代码如清单 39所示。
3. 当Task 2被删除后，Task 1再次成为最高优先级任务，因此继续执行——此时它调用vTaskDelay()来阻塞一小段时间。
4. 当Task 1处于阻塞状态时，空闲任务执行并释放了分配给现在已删除的Task 2的内存。
5. 当Task 1离开阻塞状态时，它再次成为最高优先级的就绪状态任务，因此抢占了空闲任务。当它进入运行状态时，它再次创建Task 2，如此往复。

---

```c
int main( void )
{
    /* Create the first task at priority 1. The task parameter is not used
    so is set to NULL. The task handle is also not used so likewise is set
    to NULL. */
    xTaskCreate( vTask1, "Task 1", 1000, NULL, 1, NULL );
    /* The task is created at priority 1 ______^. */
    /* Start the scheduler so the task starts executing. */
    vTaskStartScheduler();
    /* main() should never reach here as the scheduler has been started. */
    for( ;; );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单37. 示例9中main()函数的实现。</div> </center>

---

```c
TaskHandle_t xTask2Handle = NULL;
void vTask1( void *pvParameters )
{
    const TickType_t xDelay100ms = pdMS_TO_TICKS( 100UL );
    for( ;; )
    {
        /* Print out the name of this task. */
        vPrintString( "Task 1 is running\r\n" );
        /* Create task 2 at a higher priority. Again the task parameter is not
        used so is set to NULL - BUT this time the task handle is required so
        the address of xTask2Handle is passed as the last parameter. */
        xTaskCreate( vTask2, "Task 2", 1000, NULL, 2, &xTask2Handle );
        /* The task handle is the last parameter _____^^^^^^^^^^^^^ */
        /* Task 2 has/had the higher priority, so for Task 1 to reach here Task 2
        must have already executed and deleted itself. Delay for 100
        milliseconds. */
        vTaskDelay( xDelay100ms );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单38. 示例9中任务1的实现。</div> </center>

---

```c
void vTask2( void *pvParameters )
{
    /* Task 2 does nothing but delete itself. To do this it could call vTaskDelete()
    using NULL as the parameter, but instead, and purely for demonstration purposes,
    it calls vTaskDelete() passing its own task handle. */
    vPrintString( "Task 2 is running and about to delete itself\r\n" );
    vTaskDelete( xTask2Handle );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单39. 示例9中任务2的实现。</div> </center>

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure24.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图24. 执行示例9时产生的输出。</div> </center> 

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure25.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图25. 示例9的执行顺序。</div> </center>

### 线程本地存储

待定。此部分将在最终发布之前编写。

### 调度算法

#### 任务状态和事件回顾

实际运行（使用处理时间）的任务处于运行状态。在单核处理器上，任何时刻只能有一个任务处于运行状态。

不实际运行，但既不处于Blocked状态也不处于Suspended状态的任务处于就绪状态。就绪状态的任务可被调度器选择为要进入运行状态的任务。调度器将始终选择最高优先级的就绪状态任务以进入运行状态。

任务可以在Blocked状态等待事件，并在事件发生时自动返回到Ready状态。时间事件发生在特定时间，例如，当阻塞时间到期时，通常用于实现周期性或超时行为。同步事件发生在任务或中断服务例程使用任务通知、队列、事件组或众多类型的信号量发送信息时。通常用于信号异步活动，例如数据到达外设。

#### 配置调度算法

调度算法是决定将哪个就绪状态任务过渡到运行状态的软件例程。

到目前为止，所有示例都使用相同的调度算法，但可以使用`configUSE_PREEMPTION`和`configUSE_TIME_SLICING`配置常量来更改算法。这两个常量在FreeRTOSConfig.h中定义。

第三个配置常量`configUSE_TICKLESS_IDLE`也会影响调度算法，因为它的使用可能导致滴答中断在较长时间内完全关闭。configUSE_TICKLESS_IDLE是专门用于需要最小化功耗的应用程序的高级选项。configUSE_TICKLESS_IDLE在第10章“低功耗支持”中有描述。在本节提供的描述中，假设configUSE_TICKLESS_IDLE设置为0，如果未定义该常量，则为默认设置。

在所有可能的配置中，FreeRTOS调度器将确保共享优先级的任务按顺序选择进入运行状态。这种“轮流进行”的策略通常称为“轮流调度”（Round Robin Scheduling）。Round Robin调度算法不保证相同优先级的任务之间的时间均匀共享，只保证相同优先级的Ready状态任务将轮流进入Running状态。

#### 具有时间片的优先级抢占式调度

在表格 14中显示的配置将FreeRTOS调度器设置为使用称为“固定优先级的带时间片的抢占式调度”的调度算法，这是大多数小型RTOS应用程序使用的调度算法，也是迄今为止本书中所有示例使用的算法。表15提供了该算法名称中使用的术语的描述。

| 常量 | 数值                                                        |
| -------------- | ------------------------------------------------------------ |
| configUSE_PREEMPTION | 1 |
|         configUSE_TIME_SLICING       | 1       |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 14. 内核使用带时间片的优先级抢占式调度的FreeRTOSConfig.h设置。</div> </center>

| 术语 | 定义                                                        |
| -------------- | ------------------------------------------------------------ |
| Fixed Priority | 被描述为“固定优先级”的调度算法不会改变被调度任务的分配优先级，但也不会阻止任务自身改变其自己的优先级或其他任务的优先级。 |
| Pre-emptive       | 抢占式调度算法会立即“抢占”运行状态的任务，如果一个具有比运行状态任务更高优先级的任务进入了就绪状态。被抢占意味着被无意识地（在没有明确让出或阻塞的情况下）从运行状态移出，并进入就绪状态，以允许其他任务进入运行状态。 |
|Time Slicing|时间片用于在具有相同优先级的任务之间共享处理时间，即使这些任务没有明确地让出或进入阻塞状态。被描述为使用“时间片”的调度算法将在每个时间片的结束处选择一个新的任务进入运行状态，如果有其他具有与运行任务相同优先级的就绪状态任务。一个时间片等于两次RTOS滴答中断之间的时间。|

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 15. 对于描述调度策略的术语的解释。</div> </center>

图 26和图 27演示了在使用固定优先级抢占式调度和时间片算法时，任务如何被调度。图 26显示了当应用程序中的所有任务具有唯一优先级时，任务被选择进入运行状态的顺序。图 27显示了当应用程序中的两个任务共享一个优先级时，任务被选择进入运行状态的顺序。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure26.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图26. 在一个假设的应用程序中，每个任务都被分配了唯一优先级，执行模式突出显示了任务的优先级和抢占。</div> </center>

参考图 26：

1. 空闲任务

   空闲任务以最低优先级运行，所以每当一个更高优先级的任务进入就绪状态时，空闲任务都会被抢占，例如在t3、t5和t9时刻。

2. 任务3 

   任务3是一个事件驱动的任务，具有相对较低的优先级，但高于空闲任务的优先级。它大部分时间都处于Blocked状态，等待它感兴趣的事件，每当事件发生时，它就会从Blocked状态转换为Ready状态。所有FreeRTOS任务间通信机制（任务通知、队列、信号量、事件组等）都可以用来信号事件并以这种方式解除任务的阻塞状态。

   事件发生在t3和t5时刻，还有在t9到t12之间的某个时刻。在t3和t5时刻，事件会立即处理，因为此时任务3是能够运行的最高优先级任务。而在t9到t12之间的时刻，直到此时，更高优先级的任务任务1和任务2仍在执行，因此事件直到t12时才被处理，因为直到t12时，任务1和任务2都进入了Blocked状态，使得任务3成为最高优先级的就绪状态任务。

3. 任务2

   任务2是一个周期性任务，以高于任务3的优先级但低于任务1的优先级执行。该任务的周期性间隔意味着任务2希望在t1、t6和t9时刻执行。 在t6时刻，任务3处于运行状态，但任务2具有更高的相对优先级，因此抢占了任务3并立即开始执行。任务2在t7时刻完成其处理并重新进入Blocked状态，此时任务3可以重新进入Running状态以完成其处理。任务3自身在t8时刻阻塞。

4. 任务1

   任务1也是一个事件驱动的任务。它具有所有任务中最高的优先级，因此可以抢占系统中的任何其他任务。所示的唯一任务1事件发生在t10时刻，此时任务1抢占了任务2。只有任务1在t11时刻重新进入Blocked状态后，任务2才能完成其处理。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure27.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图27. 在一个假设的应用程序中，有两个任务以相同优先级运行时，执行模式突出显示了任务的优先级和时间片调度。</div> </center>

关于图 27：

1. 空闲任务和任务2

   空闲任务和任务2都是连续处理任务，它们的优先级都为0（最低优先级）。调度器只在没有更高优先级的任务能够运行时，才分配处理时间给优先级0的任务，并通过时间分片共享分配给优先级0任务时间。在图 27中，每次滴答中断时都会开始一个新的时间片，分别在时刻t1、t2、t3、t4、t5、t8、t9、t10和t11

   空闲任务和任务2轮流进入运行状态，这可能导致它们在同一个时间片的一部分时间内都处于运行状态，就像在t5和t8之间发生的情况一样。

2. 任务1

   任务1的优先级高于空闲任务。任务1是一个事件驱动的任务，大部分时间都处于Blocked状态，等待其感兴趣的事件，每当事件发生时，它就从Blocked状态转换为Ready状态。 感兴趣的事件发生在t6时刻，因此在t6时刻，任务1成为能够运行的最高优先级任务，因此任务1会在时间片的一部分内抢占空闲任务。事件的处理在t7时刻完成，此时任务1重新进入Blocked状态。

图 27显示了空闲任务与应用程序编写者创建的任务共享处理时间。如果应用程序编写者创建的空闲优先级任务有工作要做，但空闲任务没有工作，那么分配这么多处理时间给空闲任务可能不是一个理想的选择。`configIDLE_SHOULD_YIELD`编译时配置常量可以用于更改空闲任务的调度方式：

- 如果configIDLE_SHOULD_YIELD设置为0，则空闲任务将在其整个时间片内保持在运行状态，除非被更高优先级的任务抢占。
- 如果configIDLE_SHOULD_YIELD设置为1，则空闲任务会在其循环的每次迭代中放弃（自愿放弃其分配的剩余时间片），如果有其他空闲优先级任务处于Ready状态。

图 27中显示的执行模式是在configIDLE_SHOULD_YIELD设置为0时观察到的。在相同情况下，当configIDLE_SHOULD_YIELD设置为1时，将观察到图 28中显示的执行模式。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure28.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图28. 在与图27相同的情景下，但这次将configIDLE_SHOULD_YIELD设置为1时，执行模式如上所示。</div> </center>

图 28还显示了，当将configIDLE_SHOULD_YIELD设置为1时，选择进入Running状态的任务不会在整个时间片内执行，而是在Idle任务放弃的时间片内执行剩余部分。

#### 优先级抢占调度（不使用时间片）

优先级抢占调度（不使用时间片）保持了与前一节描述的相同的任务选择和抢占算法，但不使用时间片来在相同优先级的任务之间共享处理时间。

配置FreeRTOS调度器以使用不使用时间片的优先级抢占调度的FreeRTOSConfig.h设置如下表16所示。

| 常量                   | 数值 |
| ---------------------- | ---- |
| configUSE_PREEMPTION   | 1    |
| configUSE_TIME_SLICING | 0    |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 16. FreeRTOS调度器以不使用时间片的优先级抢占调度的FreeRTOSConfig.h设置。</div> </center>

如图 27所示，如果使用时间片，并且有多个处于最高优先级且能够运行的Ready状态任务，那么调度器将在每个RTOS滴答中断（标志着时间片结束）期间选择一个新的任务进入Running状态。如果不使用时间片，则调度器仅在以下情况之一时选择新任务进入Running状态：

- 更高优先级的任务进入Ready状态。

- 处于Running状态的任务进入Blocked或Suspended状态。

当不使用时间片时，任务的上下文切换次数较少，比使用时间片时的开销要小。因此，关闭时间片会减少调度器的处理开销。然而，关闭时间片也可能导致相同优先级的任务获得极不相等的处理时间，这一情况由图 29所示。因此，运行不使用时间片的调度器被认为是一种高级技术，只应由有经验的用户使用。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure29.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图29. 执行模式示例，展示了当不使用时间片时，具有相同优先级的任务可以获得极不相等的处理时间。</div> </center>

参考图 29，假设configIDLE_SHOULD_YIELD设置为0：

1. 滴答中断

   滴答中断发生在t1、t2、t3、t4、t5、t8、t11、t12和t13时刻。

2. 任务1

   任务1是一个高优先级的事件驱动任务，大部分时间都在Blocked状态等待其感兴趣的事件。每当事件发生时，任务1从Blocked状态转换到Ready状态（随后，作为最高优先级的Ready状态任务，进入Running状态）。图 29显示了任务1在t6到t7之间以及t9到t10之间处理一个事件。

3. 空闲任务和任务2

   空闲任务和任务2都是连续处理任务，并且都具有优先级0（空闲优先级）。连续处理任务不会进入Blocked状态。

   由于不使用时间片，因此处于Running状态的空闲优先级任务将保持在Running状态，直到被更高优先级的任务1抢占。

   在图 29中，空闲任务从t1开始运行，并保持在Running状态，直到在t6被任务1抢占——这是在进入Running状态后经过四个完整滴答周期以上的时间。

   任务2在t7时开始运行，这是任务1重新进入Blocked状态等待另一个事件的时间。任务2保持在Running状态，直到在t9被任务1抢占——这是任务2进入Running状态后不到一个滴答周期的时间。

   在t10时，空闲任务重新进入Running状态，尽管它已经获得了比任务2多四倍多的处理时间。

#### 协作式调度

本书侧重于抢占式调度，但FreeRTOS也可以使用协作式调度。配置FreeRTOS调度器以使用协作式调度的FreeRTOSConfig.h设置如下表17所示。

| 常量                   | 数值   |
| ---------------------- | ------ |
| configUSE_PREEMPTION   | 0      |
| configUSE_TIME_SLICING | 任意值 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 17. FreeRTOS调度器使用协作式调度的FreeRTOSConfig.h设置。</div> </center>

当使用协作式调度器时，只有在Running状态任务进入Blocked状态或Running状态任务明确地让出（通过调用taskYIELD()手动请求重新调度）时，才会发生上下文切换。任务不会被抢占，因此不能使用时间片。

图 30演示了协作式调度器的行为。图 30中的水平虚线显示了任务处于Ready状态的时间。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure30.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图30. 执行模式演示协作式调度器的行为。</div> </center>

参考图 30：

1. 任务1

   任务1具有最高的优先级。它开始于Blocked状态，等待一个信号量。

   在t3时，一个中断提供了信号量，导致任务1离开Blocked状态并进入Ready状态（从中断中提供信号量在第6章中讨论过）。

   在t3时，任务1是最高优先级的Ready状态任务，如果使用抢占式调度器，任务1将成为Running状态任务。然而，由于使用的是协作式调度器，任务1保持在Ready状态直到t4时——这是Running状态任务调用taskYIELD()的时间。

2. 任务2

   任务2的优先级介于任务1和任务3之间。它开始于Blocked状态，等待任务3在t2时发送给它的消息。

   在t2时，任务2是最高优先级的Ready状态任务，如果使用抢占式调度器，任务2将成为Running状态任务。然而，由于使用的是协作式调度器，任务2保持在Ready状态，直到Running状态任务进入Blocked状态或调用taskYIELD()。

   Running状态任务在t4时调用taskYIELD()，但在那时，任务1是最高优先级的Ready状态任务，因此任务2直到任务1在t5时重新进入Blocked状态才实际成为Running状态任务。

   在t6时，任务2重新进入Blocked状态等待下一个消息，此时任务3再次成为最高优先级的Ready状态任务。

在多任务应用程序中，应用程序编写人员必须小心，确保一个资源不会同时被多个任务访问，因为同时访问可能会损坏资源。以一个串行通信端口（UART）作为被访问资源的示例。有两个任务，任务1和任务2，正在向UART写入字符串：

1. 任务1处于运行状态并开始写入其字符串。它向UART写入“abcdefg”，但在写入任何其他字符之前离开了运行状态。
2. 任务2进入运行状态并将“123456789”写入UART，然后离开运行状态。
3. 任务1重新进入运行状态并继续向UART写入其字符串的其余字符。

在这种情况下，实际发送到UART的内容是“abcdefg123456789hijklmnop”。任务1写入的字符串未按预期以连续的顺序发送到UART。相反，它已经被损坏，因为任务2写入的字符串插入其中。

通常，在使用协作调度器时，避免由同时访问引起的问题比在使用抢占调度器时更容易[^8]：

- 当使用抢占调度器时，运行状态任务可以在任何时候被抢占，包括当它与另一个任务共享的资源处于不一致状态时。就像UART示例刚刚演示的那样，将资源保留在不一致状态可能会导致数据损坏。
- 当使用协作调度器时，应用程序编写人员可以控制切换到另一个任务的发生时机。因此，应用程序编写人员可以确保在资源处于不一致状态时不会发生任务切换。
- 在上述UART示例中，应用程序编写人员可以确保任务1在将其整个字符串写入UART之前不会离开运行状态，从而消除了其字符串被其他任务的活动损坏的可能性。

正如图 30所示，在使用协作调度器时，系统的响应速度会比使用抢占调度器时慢：

- 当使用抢占调度器时，调度器将立即开始运行成为最高优先级Ready状态任务的任务。这在必须在定义的时间段内响应高优先级事件的实时系统中通常是必要的。
- 当使用协作调度器时，切换到已成为最高优先级Ready状态任务的任务将不会在运行状态任务进入Blocked状态或调用taskYIELD()之前执行。

[^8]:在本书的后面将介绍安全地在任务之间共享资源的方法。FreeRTOS本身提供的资源，如队列和信号量，始终可以在任务之间安全共享。

## 队列管理

### 章节介绍和范围

"Queues" 提供了任务间、任务与中断之间以及中断与任务之间的通信机制。 

#### 范围

本章旨在使读者充分理解以下内容：

- 如何创建队列。
- 队列如何管理其包含的数据。
- 如何将数据发送到队列。
- 如何从队列接收数据。
- 阻塞队列的含义。
- 如何在多个队列上阻塞。
- 如何覆盖队列中的数据。
- 如何清除队列。
- 在写入和读取队列时任务优先级的影响。

本章仅涵盖任务间的通信。任务到中断和中断到任务的通信在第6章中介绍。

### 队列的特性

#### 数据存储

队列可以容纳有限数量的固定大小数据项。队列可以容纳的最大项数称为其“长度”。队列的长度和每个数据项的大小都在创建队列时设置。

队列通常用作先进先出（FIFO）缓冲区，其中数据被写入队列的末尾（尾部）并从队列的前端（头部）移除。图 31演示了数据被写入和从队列中读取，该队列被用作FIFO。还可以写入队列的前端，并覆盖已在队列前端的数据。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure31.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图31. 队列的写入和读取示例序列。</div> </center>

有两种方式可以实现队列行为：

1. 拷贝队列

   拷贝队列意味着发送到队列的数据会逐字节地被复制到队列中。

2. 引用队列

   引用队列意味着队列仅保存对发送到队列的数据的指针，而不保存数据本身。

FreeRTOS使用了拷贝队列的方法。拷贝队列被认为比引用队列更强大且更简单，因为：

- 栈变量可以直接发送到队列，即使在声明它的函数退出后，该变量将不再存在。
- 可以将数据发送到队列，而无需首先分配一个缓冲区来保存数据，然后将数据复制到已分配的缓冲区中。
- 发送任务可以立即重新使用发送到队列的变量或缓冲区。
- 发送任务和接收任务完全解耦 - 应用程序设计者无需担心哪个任务“拥有”数据，或哪个任务负责释放数据。
- 拷贝队列不会阻止队列同时用于引用队列。例如，当要排队的数据的大小使得将数据复制到队列中变得不切实际时，可以将数据的指针复制到队列中。
- RTOS完全负责分配用于存储数据的内存。
- 在受内存保护的系统中，任务可以访问的RAM将受到限制。在这种情况下，只有在发送任务和接收任务都可以访问存储数据的RAM时，才能使用引用队列。而拷贝队列不会强加这种限制；内核始终以完全特权运行，允许使用在内存保护边界上跨越传递数据的队列。

#### 多任务访问

队列是独立的对象，任何知道它们存在的任务或ISR都可以访问它们。任意数量的任务可以写入同一个队列，也可以从同一个队列中读取数据。在实际应用中，一个队列通常有多个写入者，但很少有多个读取者。

#### 队列读取时的阻塞

当任务尝试从队列中读取数据时，它可以选择指定一个“阻塞”时间。这是任务将被保持在阻塞状态以等待从队列中可用的数据的时间，如果队列已经为空的话。当另一个任务或中断将数据放入队列时，处于阻塞状态的任务会自动从阻塞状态移动到就绪状态。如果指定的阻塞时间在数据可用之前到期，任务也会自动从阻塞状态移动到就绪状态。

队列可以有多个读取者，因此一个单一的队列可能会有多个任务在等待数据时被阻塞。在这种情况下，当数据变为可用时，只会解除一个任务的阻塞。被解除阻塞的任务将始终是等待数据的最高优先级任务。如果被阻塞的任务具有相同的优先级，那么等待数据时间最长的任务将被解除阻塞。

#### 队列写入时的阻塞

与从队列中读取数据时一样，任务在向队列写入数据时也可以选择指定一个阻塞时间。在这种情况下，阻塞时间是任务应该在阻塞状态中等待队列中出现可用空间的最长时间，如果队列已经满了的话。

队列可以有多个写入者，因此一个已满的队列可能会有多个任务在等待完成发送操作时被阻塞。在这种情况下，当队列上的空间可用时，只会解除一个任务的阻塞。被解除阻塞的任务将始终是等待空间的最高优先级任务。如果被阻塞的任务具有相同的优先级，那么等待空间时间最长的任务将被解除阻塞。

#### 在多个队列上阻塞

队列可以分组成集合，允许任务进入阻塞状态，等待集合中任何一个队列上的数据变得可用。队列集合在第3.6节“从多个队列接收”中进行了演示。

### 使用队列

#### `xQueueCreate()` API 函数

队列必须在使用之前显式创建。

队列通过句柄引用，句柄的类型是 `QueueHandle_t`。xQueueCreate() API 函数创建一个队列并返回一个 QueueHandle_t，用于引用它所创建的队列。

FreeRTOS在创建队列时从FreeRTOS堆中分配RAM。这些RAM用于保存队列的数据结构和包含在队列中的项目。如果没有足够的堆RAM可用于创建队列，xQueueCreate()将返回NULL。第2章提供了有关FreeRTOS堆的更多信息。

---

```c
QueueHandle_t xQueueCreate( UBaseType_t uxQueueLength, UBaseType_t uxItemSize );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单40. xQueueCreate() API 函数原型。</div> </center>

| 参数名/ 返回值 | 描述                                                         |
| :------------: | :----------------------------------------------------------: |
| uxQueueLength | 正在创建的队列在任何时刻可以容纳的最大项目数。 |
|         uxItemSize       | 每个可存储在队列中的数据项的字节数。 |
|        返回值        | 如果返回NULL，那么队列无法创建，因为没有足够的堆内存供FreeRTOS分配队列数据结构和存储区域。 |
|                | 返回非NULL值表示成功创建了队列。返回的值应该作为已创建队列的句柄进行存储。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 18. xQueueCreate()参数和返回值。</div> </center>

在创建队列之后，可以使用`xQueueReset()` API函数将队列重置为其原始的空状态。

正如可以预期的那样，`xQueueSendToBack() `用于将数据发送到队列的后面（尾部），`xQueueSendToFront()` 用于将数据发送到队列的前面（头部）。 `xQueueSend()` 与 xQueueSendToBack() 完全相同。 

注意：不要从中断服务例程中调用 xQueueSendToFront() 或 xQueueSendToBack()。应使用中断安全版本 `xQueueSendToFrontFromISR()` 和 `xQueueSendToBackFromISR()` 代替。这些在第6章中描述。

---

```c
BaseType_t xQueueSendToFront( QueueHandle_t xQueue,
                              const void * pvItemToQueue,
                              TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单41. xQueueSendToFront() API 函数原型。</div> </center>

---

```c
BaseType_t xQueueSendToBack( QueueHandle_t xQueue,
                             const void * pvItemToQueue,
                             TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单42. xQueueSendToBack() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     xQueue     |        要将数据发送（写入）到的队列的句柄。队列句柄将是从调用 xQueueCreate() 创建队列时返回的。        |
| pvItemToQueue  |             要复制到队列中的数据的指针。             |
||队列可以容纳的每个项目的大小在创建队列时设置，因此将从 pvItemToQueue 复制这么多字节到队列存储区域。|
|  xTicksToWait  | 如果队列已经满了,任务应保持在阻塞状态以等待队列上的空间变得可用的最长时间。 |
||如果 xTicksToWait 为零并且队列已经满了，xQueueSendToFront() 和 xQueueSendToBack() 都会立即返回。|
||阻塞时间以滴答周期指定，因此它表示的绝对时间取决于滴答频率。宏 pdMS_TO_TICKS() 可用于将以毫秒指定的时间转换为以滴答为单位指定的时间。|
||将 xTicksToWait 设置为 portMAX_DELAY 将导致任务无限期地等待（不会超时），前提是在 FreeRTOSConfig.h 中将 INCLUDE_vTaskSuspend 设置为 1。|
|     返回值     | 有两种可能的返回值： |
||1. pdPASS 只有在数据成功发送到队列时才会返回 pdPASS。 如果指定了阻塞时间（xTicksToWait 不为零），则在函数返回之前，调用任务可能会被置于阻塞状态，等待队列中有空间可用，但在阻塞时间过期之前，数据已成功写入队列。|
||2. errQUEUE_FULL 如果无法将数据写入队列，因为队列已满，将返回 errQUEUE_FULL。 如果指定了阻塞时间（xTicksToWait 不为零），则在等待另一个任务或中断在队列中腾出空间之前，调用任务将被置于阻塞状态，但在这之前指定的阻塞时间已过期。|

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 19. xQueueSendToFront() 和xQueueSendToBack() 参数和返回值。</div> </center>

#### `xQueueReceive()` API 函数

xQueueReceive() 用于从队列接收（读取）一个项目。接收的项目将从队列中移除。 

注意：不要从中断服务例程中调用 xQueueReceive()。可以在第6章中找到适用于中断的安全版本 `xQueueReceiveFromISR()` 的API函数。

---

```c
BaseType_t xQueueReceive( QueueHandle_t xQueue,
                          void * const pvBuffer,
                          TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单43. xQueueReceive() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     xQueue     | 正在接收数据（读取）的队列的句柄。队列句柄将在调用 xQueueCreate() 创建队列时返回。 |
|    pvBuffer    |           指向接收数据将被复制到其中的内存的指针。           |
|                | 队列保存的每个数据项的大小在创建队列时设置。由 pvBuffer 指向的内存必须至少足够大以容纳那么多字节。 |
|  xTicksToWait  | 如果队列已经为空,任务应该在阻塞状态等待队列上可用数据的最大时间量。 |
|                | 如果 xTicksToWait 为零并且队列已经为空，则 xQueueReceive() 会立即返回。 |
|                | 阻塞时间以滴答周期为单位指定，因此它表示的绝对时间取决于滴答频率。宏 pdMS_TO_TICKS() 可用于将以毫秒指定的时间转换为以滴答为单位指定的时间。 |
|                | 如果在 FreeRTOSConfig.h 中将 xTicksToWait 设置为 portMAX_DELAY，那么任务将无限期等待（不会超时），前提是 INCLUDE_vTaskSuspend 在 FreeRTOSConfig.h 中设置为 1。 |
|     返回值     |                     有两种可能的返回值：                     |
|                | 1. pdPASS 只有在成功地从队列中读取数据时才会返回 pdPASS。 如果指定了阻塞时间（xTicksToWait 不为零），则调用任务可能会进入阻塞状态，等待队列中的数据可用，但在阻塞时间到期之前，数据已成功从队列中读取。 |
|                | 2. errQUEUE_EMPTY 如果无法从队列中读取数据，因为队列已经为空，将返回 errQUEUE_EMPTY。 如果指定了阻塞时间（xTicksToWait 不为零），则调用任务将进入阻塞状态，等待另一个任务或中断将数据发送到队列中，但在此之前阻塞时间已过。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 20. xQueueReceive() 参数和返回值。</div> </center>

#### `uxQueueMessagesWaiting()` API 函数

uxQueueMessagesWaiting() 用于查询当前队列中的项目数量。

注意：不要从中断服务例程中调用 uxQueueMessagesWaiting()。应使用中断安全的 `uxQueueMessagesWaitingFromISR()` 代替。

---

```c
UBaseType_t uxQueueMessagesWaiting( QueueHandle_t xQueue );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单44. uxQueueMessagesWaiting() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     xQueue     | 要查询的队列的句柄。该队列句柄将从调用用于创建队列的 xQueueCreate() 返回。 |
|     返回值     |  查询的队列当前持有的项目数量。如果返回零，则表示队列为空。  |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 21. uxQueueMessagesWaiting() 参数和返回值。</div> </center>

#### 示例10.从队列接收时阻塞

这个示例演示了创建队列，从多个任务向队列发送数据，以及从队列接收数据。队列被创建来保存int32_t类型的数据项。向队列发送数据的任务没有指定阻塞时间，而从队列接收数据的任务指定了阻塞时间。

向队列发送数据的任务的优先级低于从队列接收数据的任务的优先级。这意味着队列永远不会包含多个项目，因为一旦数据被发送到队列，接收任务将解除阻塞，抢占发送任务，并移除数据，使队列再次为空。

清单 45显示了写入队列的任务的实现。创建了两个这种任务的实例，一个不断将值100写入队列，另一个不断将值200写入同一个队列。任务参数用于将这些值传递到每个任务实例中。

---

```c
static void vSenderTask( void *pvParameters )
{
    int32_t lValueToSend;
    BaseType_t xStatus;
    /* Two instances of this task are created so the value that is sent to the
    queue is passed in via the task parameter - this way each instance can use
    a different value. The queue was created to hold values of type int32_t,
    so cast the parameter to the required type. */
    lValueToSend = ( int32_t ) pvParameters;
    /* As per most tasks, this task is implemented within an infinite loop. */
    for( ;; )
    {
        /* Send the value to the queue.
        The first parameter is the queue to which data is being sent. The
        queue was created before the scheduler was started, so before this task
        started to execute.
        The second parameter is the address of the data to be sent, in this case
        the address of lValueToSend.
        The third parameter is the Block time – the time the task should be kept
        in the Blocked state to wait for space to become available on the queue
        should the queue already be full. In this case a block time is not
        specified because the queue should never contain more than one item, and
        therefore never be full. */
        xStatus = xQueueSendToBack( xQueue, &lValueToSend, 0 );
        if( xStatus != pdPASS )
        {
            /* The send operation could not complete because the queue was full -
            this must be an error as the queue should never contain more than
            one item! */
            vPrintString( "Could not send to the queue.\r\n" );
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单45. 示例10中使用的发送任务的实现。</div> </center>

清单 46展示了接收数据的任务的实现。接收任务指定了100毫秒的阻塞时间，因此会进入阻塞状态等待数据可用。只有当队列中有数据可用或者等待了100毫秒后仍然没有数据可用时，它才会离开阻塞状态。在这个示例中，由于有两个任务不断向队列中写入数据，因此100毫秒的超时应该永远不会到期。

---

```c
static void vReceiverTask( void *pvParameters )
{
    /* Declare the variable that will hold the values received from the queue. */
    int32_t lReceivedValue;
    BaseType_t xStatus;
    const TickType_t xTicksToWait = pdMS_TO_TICKS( 100 );
    /* This task is also defined within an infinite loop. */
    for( ;; )
    {
        /* This call should always find the queue empty because this task will
        immediately remove any data that is written to the queue. */
        if( uxQueueMessagesWaiting( xQueue ) != 0 )
        {
        vPrintString( "Queue should have been empty!\r\n" );
        }
        /* Receive data from the queue.
        The first parameter is the queue from which data is to be received. The
        queue is created before the scheduler is started, and therefore before this
        task runs for the first time.
        The second parameter is the buffer into which the received data will be
        placed. In this case the buffer is simply the address of a variable that
        has the required size to hold the received data.
        The last parameter is the block time – the maximum amount of time that the
        task will remain in the Blocked state to wait for data to be available
        should the queue already be empty. */
        xStatus = xQueueReceive( xQueue, &lReceivedValue, xTicksToWait );
        if( xStatus == pdPASS )
        {
            /* Data was successfully received from the queue, print out the received
            value. */
            vPrintStringAndNumber( "Received = ", lReceivedValue );
        }
        else
        {
            /* Data was not received from the queue even after waiting for 100ms.
            This must be an error as the sending tasks are free running and will be
            continuously writing to the queue. */
            vPrintString( "Could not receive from the queue.\r\n" );
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单46. 示例10中使用的接收任务的实现。</div> </center>

清单 47包含了`main()`函数的定义。这个函数在启动调度器之前，简单地创建了队列和三个任务。队列被创建为最多可以容纳五个int32_t值，尽管任务的优先级被设置为队列永远不会同时包含多个项。

---

```c
/* Declare a variable of type QueueHandle_t. This is used to store the handle
to the queue that is accessed by all three tasks. */
QueueHandle_t xQueue;
int main( void )
{
    /* The queue is created to hold a maximum of 5 values, each of which is
    large enough to hold a variable of type int32_t. */
    xQueue = xQueueCreate( 5, sizeof( int32_t ) );
    if( xQueue != NULL )
    {
        /* Create two instances of the task that will send to the queue. The task
        parameter is used to pass the value that the task will write to the queue,
        so one task will continuously write 100 to the queue while the other task
        will continuously write 200 to the queue. Both tasks are created at
        priority 1. */
        xTaskCreate( vSenderTask, "Sender1", 1000, ( void * ) 100, 1, NULL );
        xTaskCreate( vSenderTask, "Sender2", 1000, ( void * ) 200, 1, NULL );
        /* Create the task that will read from the queue. The task is created with
        priority 2, so above the priority of the sender tasks. */
        xTaskCreate( vReceiverTask, "Receiver", 1000, NULL, 2, NULL );
        /* Start the scheduler so the created tasks start executing. */
        vTaskStartScheduler();
    }
    else
    {
        /* The queue could not be created. */
    }
    /* If all is well then main() will never reach here as the scheduler will
    now be running the tasks. If main() does reach here then it is likely that
    there was insufficient FreeRTOS heap memory available for the idle task to be
    created. Chapter 2 provides more information on heap memory management. */
    for( ;; );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单47. 示例10中main()函数的实现。</div> </center>

两个发送到队列的任务具有相同的优先级。这会导致这两个发送任务轮流向队列发送数据。示例10产生的输出如图 32所示。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure32.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图32. 执行示例10产生的输出。</div> </center>

图 33展示了执行的顺序。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure33.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图33. 示例10运行的执行顺序。</div> </center>

### 从多个源接收数据

在FreeRTOS设计中，常见的情况是一个任务需要从多个源接收数据。接收任务需要知道数据的来源，以确定如何处理数据。一个简单的设计解决方案是使用一个队列来传输结构体，结构体的字段中包含了数据的值和数据的来源。这个方案在图 34中进行了演示。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure34.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图34. 一个结构体被发送到队列的示例场景。</div> </center>

关于图 34的说明：

- 创建了一个队列，该队列可以容纳Data_t类型的结构体。结构体的成员允许在一个消息中发送数据值和表示数据含义的枚举类型。
- 使用一个中央控制器任务来执行主要的系统功能。该任务必须对通过队列传递的输入和系统状态的变化做出反应。
- 使用CAN总线任务来封装CAN总线接口功能。当CAN总线任务接收并解码消息时，它会将已解码的消息以Data_t结构体的形式发送给控制器任务。传输结构体的eDataID成员用于让控制器任务知道数据是什么，即在所示情况下是电机速度值。传输结构体的lDataValue成员用于让控制器任务知道实际的电机速度值。
- 使用人机界面（HMI）任务来封装所有的HMI功能。机器操作员可能以多种方式输入命令并查询值，这些方式必须在HMI任务中进行检测和解释。当输入新命令时，HMI任务会将命令以Data_t结构体的形式发送给控制器任务。传输结构体的eDataID成员用于让控制器任务知道数据是什么，即在所示情况下是新的设定点值。传输结构体的lDataValue成员用于让控制器任务知道实际的设定点值。

#### 示例 11. 队列发送时的阻塞，以及在队列上发送结构体

示例11与示例10类似，但任务的优先级是相反的，因此接收任务的优先级低于发送任务。此外，队列用于传递结构体，而不是整数。 清单 48显示了示例11中使用的结构的定义。

---

```c
/* Define an enumerated type used to identify the source of the data. */
typedef enum
{
    eSender1,
    eSender2
} DataSource_t;
/* Define the structure type that will be passed on the queue. */
typedef struct
{
    uint8_t ucValue;
    DataSource_t eDataSource;
} Data_t;
/* Declare two variables of type Data_t that will be passed on the queue. */
static const Data_t xStructsToSend[ 2 ] =
{
    { 100, eSender1 }, /* Used by Sender1. */
    { 200, eSender2 } /* Used by Sender2. */
};
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单48. 要传递到队列上的结构体的定义，以及用于示例的两个变量的声明。</div> </center>

在示例10中，接收任务具有最高优先级，因此队列永远不会包含多个项。这是因为接收任务一旦将数据放入队列中，就会抢占发送任务，因此队列中的数据永远只有一个。在示例11中，发送任务具有更高的优先级，因此队列通常会满。这是因为一旦接收任务从队列中删除一个项目，它就会被一个发送任务所抢占，然后立即重新填充队列。然后发送任务会重新进入阻塞状态，等待队列再次有空间可用。

清单 49显示了发送任务的实现。发送任务指定了100毫秒的阻塞时间，因此每次队列变满时它都会进入阻塞状态等待空间可用。当队列上有空间可用或者100毫秒时间已过去而没有空间可用时，它将离开阻塞状态。在这个示例中，100毫秒的超时时间应该永远不会到期，因为接收任务不断地通过从队列中移除项目来释放空间。

---

```c
static void vSenderTask( void *pvParameters )
{
    BaseType_t xStatus;
    const TickType_t xTicksToWait = pdMS_TO_TICKS( 100 );
    /* As per most tasks, this task is implemented within an infinite loop. */
    for( ;; )
    {
        /* Send to the queue.
        The second parameter is the address of the structure being sent. The
        address is passed in as the task parameter so pvParameters is used
        directly.
        The third parameter is the Block time - the time the task should be kept
        in the Blocked state to wait for space to become available on the queue
        if the queue is already full. A block time is specified because the
        sending tasks have a higher priority than the receiving task so the queue
        is expected to become full. The receiving task will remove items from
        the queue when both sending tasks are in the Blocked state. */
        xStatus = xQueueSendToBack( xQueue, pvParameters, xTicksToWait );
        if( xStatus != pdPASS )
        {
            /* The send operation could not complete, even after waiting for 100ms.
            This must be an error as the receiving task should make space in the
            queue as soon as both sending tasks are in the Blocked state. */
            vPrintString( "Could not send to the queue.\r\n" );
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单49. 示例11中使用的发送任务的实现。</div> </center>

接收任务的优先级最低，因此只有在两个发送任务都处于阻塞状态时才会运行。只有当队列已满时，发送任务才会进入阻塞状态，因此只有当队列已满时，接收任务才会执行。因此，即使没有指定阻塞时间，它始终希望接收数据。 

接收任务的实现如清单 50所示。

---

```c
static void vReceiverTask( void *pvParameters )
{
    /* Declare the structure that will hold the values received from the queue. */
    Data_t xReceivedStructure;
    BaseType_t xStatus;
    /* This task is also defined within an infinite loop. */
    for( ;; )
    {
        /* Because it has the lowest priority this task will only run when the
        sending tasks are in the Blocked state. The sending tasks will only enter
        the Blocked state when the queue is full so this task always expects the
        number of items in the queue to be equal to the queue length, which is 3 in
        this case. */
        if( uxQueueMessagesWaiting( xQueue ) != 3 )
        {
            vPrintString( "Queue should have been full!\r\n" );
        }
        /* Receive from the queue.
        The second parameter is the buffer into which the received data will be
        placed. In this case the buffer is simply the address of a variable that
        has the required size to hold the received structure.
        The last parameter is the block time - the maximum amount of time that the
        task will remain in the Blocked state to wait for data to be available
        if the queue is already empty. In this case a block time is not necessary
        because this task will only run when the queue is full. */
        xStatus = xQueueReceive( xQueue, &xReceivedStructure, 0 );
        if( xStatus == pdPASS )
        {
            /* Data was successfully received from the queue, print out the received
            value and the source of the value. */
            if( xReceivedStructure.eDataSource == eSender1 )
            {
                vPrintStringAndNumber( "From Sender 1 = ", xReceivedStructure.ucValue );
            }
            else
            {
                vPrintStringAndNumber( "From Sender 2 = ", xReceivedStructure.ucValue );
            }
        }
        else
        {
            /* Nothing was received from the queue. This must be an error as this
            task should only run when the queue is full. */
            vPrintString( "Could not receive from the queue.\r\n" );
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单50. 示例11中使用的接收任务的实现。</div> </center>

与先前的示例相比，main() 几乎没有变化。队列被创建为能够容纳三个 Data_t 结构体，并且发送任务和接收任务的优先级被颠倒了。

main() 的实现如清单 51所示。

---

```c
int main( void )
{
    /* The queue is created to hold a maximum of 3 structures of type Data_t. */
    xQueue = xQueueCreate( 3, sizeof( Data_t ) );
    if( xQueue != NULL )
    {
        /* Create two instances of the task that will write to the queue. The
        parameter is used to pass the structure that the task will write to the
        queue, so one task will continuously send xStructsToSend[ 0 ] to the queue
        while the other task will continuously send xStructsToSend[ 1 ]. Both
        tasks are created at priority 2, which is above the priority of the receiver. */
        xTaskCreate( vSenderTask, "Sender1", 1000, &( xStructsToSend[ 0 ] ), 2, NULL );
        xTaskCreate( vSenderTask, "Sender2", 1000, &( xStructsToSend[ 1 ] ), 2, NULL );
        /* Create the task that will read from the queue. The task is created with
        priority 1, so below the priority of the sender tasks. */
        xTaskCreate( vReceiverTask, "Receiver", 1000, NULL, 1, NULL );
        /* Start the scheduler so the created tasks start executing. */
        vTaskStartScheduler();
    }
    else
    {
        /* The queue could not be created. */
    }
    /* If all is well then main() will never reach here as the scheduler will
    now be running the tasks. If main() does reach here then it is likely that
    there was insufficient heap memory available for the idle task to be created.
    Chapter 2 provides more information on heap memory management. */
    for( ;; );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单51. 示例11中main()函数的实现。</div> </center>

示例11产生的输出如图 35所示。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure35.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图35. 执行示例11产生的输出。</div> </center>

图 36展示了由于将发送任务的优先级设置为高于接收任务而导致的执行顺序。表22提供了对图 36的进一步解释，并描述了为什么前四条消息来自同一个任务。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure36.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图36. 示例11运行的执行顺序。</div> </center>

| 时间点 |                             描述                             |
| :----: | :----------------------------------------------------------: |
|   t1   |            任务发送者1执行并向队列发送3个数据项。            |
|   t2   | 队列已满，所以发送者1进入了Blocked状态，等待下一次发送完成。任务发送者2现在是能够运行的最高优先级任务，所以进入了运行状态。 |
|   t3   | 任务发送者2发现队列已经满了，所以进入了Blocked状态，等待其第一次发送完成。任务接收者现在是能够运行的最高优先级任务，所以进入了运行状态。 |
|   t4   | 优先级高于接收任务的两个任务正在等待队列中的空间变得可用，这导致任务接收者一旦从队列中移除一个项目就被抢占。任务发送者1和任务发送者2具有相同的优先级，因此调度程序选择等待时间最长的任务进入运行状态，本例中是任务发送者1。 |
|   t5   | 任务发送者1再次将另一个数据项发送到队列。队列中只有一个空间，因此任务发送者1进入了阻塞状态，等待其下一次发送完成。任务接收者再次是能够运行的最高优先级任务，因此进入了运行状态。 |
||任务发送者1现在已经向队列发送了四个项目，而任务发送者2仍在等待将其第一个项目发送到队列。|
|   t6   | 优先级高于接收任务的两个任务正在等待队列上的空间，因此一旦接收任务从队列中删除了一个项目，接收任务就会被抢占。这次，发送者2等待的时间比发送者1长，所以发送者2进入了Running状态。 |
|   t7   | 任务发送者2发送一个数据项到队列中。队列中只有一个空间，所以发送者2进入了Blocked状态等待其下一个发送操作完成。发送者1和发送者2都在等待队列中有空间可用，所以只有任务接收者可以进入Running状态。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 22. 图36的关键解释。</div> </center>

### 处理大型或可变大小数据

#### 队列传输指针

如果要存储在队列中的数据较大，那么最好使用队列来传输指向数据的指针，而不是将数据本身逐字节复制到队列中再取出。传输指针在处理时间和所需RAM量方面更加高效。但是，在队列传输指针时，必须非常小心确保以下几点：

1. 明确定义RAM的所有者。 通过指针在任务之间共享内存时，必须确保两个任务不会同时修改内存内容，或者采取任何可能导致内存内容无效或不一致的其他操作。理想情况下，只有发送任务应该被允许访问内存，直到从队列中填入了指向内存的指针，只有接收任务被允许在从队列中接收指针后访问内存。
2. 指向的RAM保持有效。 如果指向的内存是动态分配的，或者从预分配缓冲池获取的，则应由一个任务负责释放内存。在释放内存后，不应有任务尝试访问该内存。 指针不应该用于访问在任务堆栈上分配的数据。在堆栈帧发生更改后，数据将不再有效。

例如，清单 52、清单 53和清单 54演示了如何使用队列从一个任务发送指向缓冲区的指针到另一个任务：

- 清单 52创建一个可以容纳多达5个指针的队列。
- 清单 53分配一个缓冲区，将一个字符串写入缓冲区，然后将指向缓冲区的指针发送到队列。
- 清单 54从队列中接收指向缓冲区的指针，然后打印缓冲区中包含的字符串。

---

```c
/* Declare a variable of type QueueHandle_t to hold the handle of the queue being created. */
QueueHandle_t xPointerQueue;
/* Create a queue that can hold a maximum of 5 pointers, in this case character pointers. */
xPointerQueue = xQueueCreate( 5, sizeof( char * ) );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单52. 创建一个包含指针的队列。</div> </center>

---

```c
/* A task that obtains a buffer, writes a string to the buffer, then sends the address of the buffer to the queue created in Listing 52. */
void vStringSendingTask( void *pvParameters )
{
    char *pcStringToSend;
    const size_t xMaxStringLength = 50;
    BaseType_t xStringNumber = 0;
    for( ;; )
    {
        /* Obtain a buffer that is at least xMaxStringLength characters big. The implementation
        of prvGetBuffer() is not shown – it might obtain the buffer from a pool of pre-allocated
        buffers, or just allocate the buffer dynamically. */
        pcStringToSend = ( char * ) prvGetBuffer( xMaxStringLength );
        /* Write a string into the buffer. */
        snprintf( pcStringToSend, xMaxStringLength, "String number %d\r\n", xStringNumber );
        /* Increment the counter so the string is different on each iteration of this task. */
        xStringNumber++;
        /* Send the address of the buffer to the queue that was created in Listing 52. The
        address of the buffer is stored in the pcStringToSend variable.*/
        xQueueSend( xPointerQueue, /* The handle of the queue. */
                   &pcStringToSend, /* The address of the pointer that points to the buffer. */
                    portMAX_DELAY );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单53. 使用队列发送指向缓存区的指针。</div> </center>

---

```c
/* A task that receives the address of a buffer from the queue created in Listing 52, and written to in Listing 53. The buffer contains a string, which is printed out. */
void vStringReceivingTask( void *pvParameters )
{
    char *pcReceivedString;
    for( ;; )
    {
        /* Receive the address of a buffer. */
        xQueueReceive( xPointerQueue, /* The handle of the queue. */
        &pcReceivedString, /* Store the buffer’s address in pcReceivedString. */
        portMAX_DELAY );
        /* The buffer holds a string, print it out. */
        vPrintString( pcReceivedString );
        /* The buffer is not required any more - release it so it can be freed, or re-used. */
        prvReleaseBuffer( pcReceivedString );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单54. 使用队列接收指向缓存区的指针。</div> </center>

#### 使用队列发送不同类型和长度的数据

前面的章节演示了两种强大的设计模式：向队列发送结构体和向队列发送指针。结合这些技术可以允许一个任务使用单一队列从任何数据源接收任何数据类型。FreeRTOS+TCP TCP/IP堆栈的实现提供了如何实现这一点的实际示例。 

TCP/IP堆栈在其自己的任务中运行，必须处理来自许多不同来源的事件。不同类型的事件与不同类型和长度的数据相关联。发生在TCP/IP任务之外的所有事件都由类型为IPStackEvent_t的结构体描述，并发送到TCP/IP任务的队列上。IPStackEvent_t结构体的示例如下所示（见清单 55）。IPStackEvent_t结构体的pvData成员是一个指针，可以直接用来保存值，或者指向一个缓冲区。

---

```c
/* A subset of the enumerated types used in the TCP/IP stack to identify events. */
typedef enum
{
    eNetworkDownEvent = 0, /* The network interface has been lost, or needs (re)connecting. */
    eNetworkRxEvent, /* A packet has been received from the network. */
    eTCPAcceptEvent, /* FreeRTOS_accept() called to accept or wait for a new client. */
    /* Other event types appear here but are not shown in this listing. */
} eIPEvent_t;
/* The structure that describes events, and is sent on a queue to the TCP/IP task. */
typedef struct IP_TASK_COMMANDS
{
    /* An enumerated type that identifies the event. See the eIPEvent_t definition above. */
    eIPEvent_t eEventType;
    /* A generic pointer that can hold a value, or point to a buffer. */
    void *pvData;
} IPStackEvent_t;
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单55. FreeRTOS+TCP中用于向TCP/IP堆栈任务发送事件的结构体。</div> </center>

示例TCP/IP事件及其关联数据包括：

- eNetworkRxEvent：从网络接收到数据包。

  从网络接收的数据使用类型为IPStackEvent_t的结构体发送到TCP/IP任务。结构体的eEventType成员设置为eNetworkRxEvent，结构体的pvData成员用于指向包含接收数据的缓冲区。伪代码示例如下（见清单 56）。

---

```c
void vSendRxDataToTheTCPTask( NetworkBufferDescriptor_t *pxRxedData )
{
    IPStackEvent_t xEventStruct;
    /* Complete the IPStackEvent_t structure. The received data is stored in
    pxRxedData. */
    xEventStruct.eEventType = eNetworkRxEvent;
    xEventStruct.pvData = ( void * ) pxRxedData;
    /* Send the IPStackEvent_t structure to the TCP/IP task. */
    xSendEventStructToIPTask( &xEventStruct );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单56. 伪代码示例，展示了如何使用IPStackEvent_t结构体将从网络接收到的数据发送到TCP/IP任务。</div> </center>

* eTCPAcceptEvent: 表示一个套接字正在接受来自客户端的连接，或正在等待连接。

  接受事件是从调用 FreeRTOS_accept() 的任务发送到 TCP/IP 任务的，使用类型为 IPStackEvent_t 的结构体。结构体的 eEventType 成员被设置为 eTCPAcceptEvent，而结构体的 pvData 成员被设置为正在接受连接的套接字的句柄。示例伪代码如下（见清单 57）

---

```c
void vSendAcceptRequestToTheTCPTask( Socket_t xSocket )
{
    IPStackEvent_t xEventStruct;
    /* Complete the IPStackEvent_t structure. */
    xEventStruct.eEventType = eTCPAcceptEvent;
    xEventStruct.pvData = ( void * ) xSocket;
    /* Send the IPStackEvent_t structure to the TCP/IP task. */
    xSendEventStructToIPTask( &xEventStruct );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单57. 伪代码示例，展示了如何使用 IPStackEvent_t 结构体将正在接受连接的套接字句柄发送到 TCP/IP 任务。</div> </center>

* eNetworkDownEvent: 表示需要连接或重新连接网络。

  网络下线事件是从网络接口发送到 TCP/IP 任务的，使用类型为 IPStackEvent_t 的结构体。结构体的 eEventType 成员被设置为 eNetworkDownEvent。网络下线事件不关联任何数据，因此结构体的 pvData 成员不被使用。以下是伪代码示例（见清单 58）

---

```c
void vSendNetworkDownEventToTheTCPTask( Socket_t xSocket )
{
    IPStackEvent_t xEventStruct;
    /* Complete the IPStackEvent_t structure. */
    xEventStruct.eEventType = eNetworkDownEvent;
    xEventStruct.pvData = NULL; /* Not used, but set to NULL for completeness. */
    /* Send the IPStackEvent_t structure to the TCP/IP task. */
    xSendEventStructToIPTask( &xEventStruct );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单58. 伪代码示例，展示了如何使用 IPStackEvent_t 结构将网络下线事件发送到 TCP/IP 任务。</div> </center>

在 TCP/IP 任务内接收和处理这些事件的代码如清单 59 所示。可以看到，从队列接收的 IPStackEvent_t 结构体的 eEventType 成员用于确定如何解释 pvData 成员。

---

```c
IPStackEvent_t xReceivedEvent;
/* Block on the network event queue until either an event is received, or xNextIPSleep ticks
pass without an event being received. eEventType is set to eNoEvent in case the call to
xQueueReceive() returns because it timed out, rather than because an event was received. */
xReceivedEvent.eEventType = eNoEvent;
xQueueReceive( xNetworkEventQueue, &xReceivedEvent, xNextIPSleep );
/* Which event was received, if any? */
switch( xReceivedEvent.eEventType )
{
    case eNetworkDownEvent :
        /* Attempt to (re)establish a connection. This event is not associated with any
        data. */
        prvProcessNetworkDownEvent();
    break;
    case eNetworkRxEvent:
        /* The network interface has received a new packet. A pointer to the received data
        is stored in the pvData member of the received IPStackEvent_t structure. Process
        the received data. */
        prvHandleEthernetPacket( ( NetworkBufferDescriptor_t * )( xReceivedEvent.pvData ) );
    break;
    case eTCPAcceptEvent:
        /* The FreeRTOS_accept() API function was called. The handle of the socket that is
        accepting a connection is stored in the pvData member of the received IPStackEvent_t
        structure. */
        xSocket = ( FreeRTOS_Socket_t * ) ( xReceivedEvent.pvData );
        xTCPCheckNewClient( pxSocket );
    break;
    /* Other event types are processed in the same way, but are not shown here. */
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单59. 如何处理和接收 IPStackEvent_t 结构的伪代码示例。</div> </center>

### 接收来自多个队列的消息

#### 队列集

通常，应用程序设计需要一个单一任务来接收不同大小、不同含义和来自不同来源的数据。前一节演示了如何通过使用一个接收结构体的单一队列以一种简洁高效的方式来实现这一点。然而，有时应用程序设计者受到限制，这些限制会限制其设计选择，需要为某些数据源使用单独的队列。例如，集成到设计中的第三方代码可能假定存在专用队列。在这种情况下，可以使用“队列集”（queue sets）。

队列集允许一个任务从多个队列接收数据，而不需要任务轮询每个队列以确定哪个（如果有的话）包含数据。

使用队列集来接收来自多个来源的数据的设计不如使用一个接收结构体的单一队列来实现的设计简洁和高效。因此，建议仅在设计限制绝对需要时才使用队列集。

以下部分描述了如何使用队列集：

1. 创建一个队列集。
2. 将队列添加到集合中。也可以将信号量添加到队列集中，信号量将在本书的后续部分描述。
3. 从队列集中读取以确定其中哪些队列包含数据。当属于集合的队列接收到数据时，接收队列的句柄会被发送到队列集，当任务调用从队列集中读取的函数时，句柄会被返回。因此，如果从队列集返回了队列句柄，那么句柄引用的队列已知包含数据，任务可以直接从队列中读取数据。

队列集功能可以通过在 FreeRTOSConfig.h 文件中将` configUSE_QUEUE_SETS `编译时配置常量设置为 1 来启用。

#### `xQueueCreateSet()` API 函数

在使用之前，必须显式地创建队列集。

队列集通过句柄引用，句柄的类型是 `QueueSetHandle_t`。xQueueCreateSet() API 函数创建一个队列集并返回一个 QueueSetHandle_t，该句柄引用了它所创建的队列集。

---

```c
QueueSetHandle_t xQueueCreateSet( const UBaseType_t uxEventQueueLength );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单60. xQueueCreateSet() API 函数原型。</div> </center>

|   参数名/ 返回值   |                             描述                             |
| :----------------: | :----------------------------------------------------------: |
| uxEventQueueLength | 当队列集中的一个队列接收到数据时，接收队列的句柄会被发送到队列集。uxEventQueueLength 定义了被创建的队列集可以同时持有的队列句柄的最大数量。 |
|                    | 只有在队列集中的某个队列接收到数据时，才会将队列句柄发送到队列集。如果集合中的所有队列都已满，则不能将任何队列句柄发送到队列集，因为队列无法接收数据。因此，队列集将同时持有的项的最大数量是集合中每个队列长度的总和。 |
|                    | 举例来说，如果集合中有三个空队列，每个队列长度为五，那么在所有队列都填满之前，集合中的队列总共可以接收十五个项（三个队列乘以每个队列的五个项）。在这个例子中，uxEventQueueLength 必须设置为十五，以确保队列集可以接收发送到它的每个项。 |
|                    | 信号量也可以添加到队列集中。二值信号量和计数信号量将在本书的后续部分介绍。用于计算所需的 uxEventQueueLength 的目的时，二值信号量的长度为一，计数信号量的长度由信号量的最大计数值决定。 |
|                    | 再举一个例子，如果一个队列集将包含一个长度为三的队列和一个二值信号量（长度为一），那么 uxEventQueueLength 必须设置为四（三加一）。 |
|       返回值       | 如果返回 NULL，则表示无法创建队列集，因为没有足够的堆内存可供FreeRTOS分配队列集的数据结构和存储区域。 |
|                    | 非NULL值的返回表示队列集已成功创建。返回的值应该存储为已创建队列集的句柄。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 23. xQueueCreateSet() 参数和返回值。</div> </center>

#### `xQueueAddToSet()` API 函数

xQueueAddToSet() 将队列或信号量添加到队列集中。 信号量将在本书后面进行描述。

---

```c
BaseType_t xQueueAddToSet( QueueSetMemberHandle_t xQueueOrSemaphore,
                           QueueSetHandle_t xQueueSet );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单61. xQueueAddToSet() API 函数原型。</div> </center>

|  参数名/ 返回值   |                             描述                             |
| :---------------: | :----------------------------------------------------------: |
| xQueueOrSemaphore |             被添加到队列集的队列或信号量的句柄。             |
|                   | 队列句柄和信号量句柄都可以强制转换为QueueSetMemberHandle_t类型。 |
|     xQueueSet     |             被添加到队列集的队列或信号量的句柄。             |
|      返回值       |                     有两种可能的返回值：                     |
|                   | 1. pdPASS 只有当队列或信号量成功添加到队列集时才会返回pdPASS。 |
|                   |  2. pdFAIL 如果队列或信号量无法添加到队列集，则返回pdFAIL。  |
|                   | 只有在它们为空时，队列和二值信号量才能添加到队列集中。只有在其计数为零时，计数信号量才能添加到队列集中。队列和信号量一次只能是一个队列的成员。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 24. xQueueAddToSet() 参数和返回值。</div> </center>

#### `xQueueSelectFromSet()` API 函数

xQueueSelectFromSet() 从队列集中读取一个队列句柄。

当属于集合的队列或信号量接收到数据时，接收队列或信号量的句柄被发送到队列集，并在任务调用xQueueSelectFromSet()时返回。如果从xQueueSelectFromSet()的调用中返回一个句柄，那么与该句柄引用的队列或信号量已知包含数据，然后调用任务必须直接从队列或信号量中读取数据。

注意：除非队列或信号量的句柄首先从xQueueSelectFromSet()的调用中返回，否则不要从集合的成员队列或信号量中读取数据。每次从xQueueSelectFromSet()的调用中返回队列句柄或信号量句柄时，只读取一个项目。

---

```c
QueueSetMemberHandle_t xQueueSelectFromSet( QueueSetHandle_t xQueueSet,
									   const TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单62. xQueueSelectFromSet() API 函数原型。</div> </center>

| 参数名/ 返回值 | 描述 |
| :------------: | :--: |
|   xQueueSet    | 接收（读取）队列句柄或信号量句柄的队列集的句柄。队列集句柄将会在调用xQueueCreateSet()创建队列集时返回。 |
|  xTicksToWait  | 如果集合中的所有队列和信号量都为空，这是调用任务保持阻塞状态等待从队列集中接收（读取）队列或信号量句柄的最长时间。 |
|                | 如果xTicksToWait为零并且集合中的所有队列和信号量都为空，xQueueSelectFromSet()将立即返回。 |
|         | 阻塞时间以时钟周期（tick）为单位指定，因此它表示的绝对时间取决于时钟周期的频率。宏pdMS_TO_TICKS()可以用来将以毫秒指定的时间转换为以时钟周期（tick）指定的时间。 |
|                | 将xTicksToWait设置为portMAX_DELAY将导致任务无限期地等待（不会超时），前提是在FreeRTOSConfig.h中设置了INCLUDE_vTaskSuspend为1。 |
|      返回值           | 如果返回值不是NULL，那么它将是一个已知包含数据的队列或信号量的句柄。如果指定了阻塞时间（xTicksToWait不为零），那么可能会将调用任务置于阻塞状态，等待集合中的队列或信号量包含可用的数据，并且在阻塞时间到期之前成功从队列集中读取了句柄。句柄以QueueSetMemberHandle_t类型返回，可以将其转换为QueueHandle_t类型或SemaphoreHandle_t类型。 |
|                | 如果返回值为NULL，则无法从队列集中读取句柄。如果指定了阻塞时间（xTicksToWait不为零），那么调用任务将被置于阻塞状态，等待另一个任务或中断发送数据到集合中的队列或信号量，但阻塞时间可能在此之前已经到期。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 25. xQueueSelectFromSet() 参数和返回值。</div> </center>

#### 示例12. 使用一个队列集

这个示例创建了两个发送任务和一个接收任务。发送任务将数据发送到接收任务的单独的两个队列，每个任务一个队列。这两个队列被添加到一个队列集中，接收任务从队列集中读取以确定哪个队列包含数据。

这些任务、队列和队列集都是在`main()`函数中创建的，可以参考实现代码中的清单 63来了解其具体实现。

---

```c
/* Declare two variables of type QueueHandle_t. Both queues are added to the same queue set. */
static QueueHandle_t xQueue1 = NULL, xQueue2 = NULL;
/* Declare a variable of type QueueSetHandle_t. This is the queue set to which the two queues are added. */
static QueueSetHandle_t xQueueSet = NULL;
int main( void )
{
    /* Create the two queues, both of which send character pointers. The priority
    of the receiving task is above the priority of the sending tasks, so the queues
    will never have more than one item in them at any one time*/
    xQueue1 = xQueueCreate( 1, sizeof( char * ) );
    xQueue2 = xQueueCreate( 1, sizeof( char * ) );
    /* Create the queue set. Two queues will be added to the set, each of which can
    contain 1 item, so the maximum number of queue handles the queue set will ever
    have to hold at one time is 2 (2 queues multiplied by 1 item per queue). */
    xQueueSet = xQueueCreateSet( 1 * 2 );
    /* Add the two queues to the set. */
    xQueueAddToSet( xQueue1, xQueueSet );
    xQueueAddToSet( xQueue2, xQueueSet );
    /* Create the tasks that send to the queues. */
    xTaskCreate( vSenderTask1, "Sender1", 1000, NULL, 1, NULL );
    xTaskCreate( vSenderTask2, "Sender2", 1000, NULL, 1, NULL );
    /* Create the task that reads from the queue set to determine which of the two
    queues contain data. */
    xTaskCreate( vReceiverTask, "Receiver", 1000, NULL, 2, NULL );
    /* Start the scheduler so the created tasks start executing. */
    vTaskStartScheduler();
    /* As normal, vTaskStartScheduler() should not return, so the following lines
    Will never execute. */
    for( ;; );
    return 0;
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单63. 示例12中main()函数的实现。</div> </center>

第一个发送任务使用xQueue1每100毫秒向接收任务发送一个字符指针。第二个发送任务使用xQueue2每200毫秒向接收任务发送一个字符指针。字符指针被设置为指向标识发送任务的字符串。这两个发送任务的实现在清单 64中展示。

---

```c
void vSenderTask1( void *pvParameters )
{
    const TickType_t xBlockTime = pdMS_TO_TICKS( 100 );
    const char * const pcMessage = "Message from vSenderTask1\r\n";
    /* As per most tasks, this task is implemented within an infinite loop. */
    for( ;; )
    {
        /* Block for 100ms. */
        vTaskDelay( xBlockTime );
        /* Send this task's string to xQueue1. It is not necessary to use a block
        time, even though the queue can only hold one item. This is because the
        priority of the task that reads from the queue is higher than the priority of
        this task; as soon as this task writes to the queue it will be pre-empted by
        the task that reads from the queue, so the queue will already be empty again
        by the time the call to xQueueSend() returns. The block time is set to 0. */
        xQueueSend( xQueue1, &pcMessage, 0 );
    }
}
/*-----------------------------------------------------------*/
void vSenderTask2( void *pvParameters )
{
    const TickType_t xBlockTime = pdMS_TO_TICKS( 200 );
    const char * const pcMessage = "Message from vSenderTask2\r\n";
    /* As per most tasks, this task is implemented within an infinite loop. */
    for( ;; )
    {
        /* Block for 200ms. */
        vTaskDelay( xBlockTime );
        /* Send this task's string to xQueue2. It is not necessary to use a block
        time, even though the queue can only hold one item. This is because the
        priority of the task that reads from the queue is higher than the priority of
        this task; as soon as this task writes to the queue it will be pre-empted by
        the task that reads from the queue, so the queue will already be empty again
        by the time the call to xQueueSend() returns. The block time is set to 0. */
        xQueueSend( xQueue2, &pcMessage, 0 );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单64. 示例12使用的发送任务的实现。</div> </center>

被发送任务写入的队列都是同一个队列集的成员。每次一个任务发送到其中一个队列时，队列的句柄都会被发送到队列集。接收任务调用xQueueSelectFromSet()来从队列集中读取队列句柄。接收任务一旦从集合中接收到一个队列句柄，就知道了由接收到的句柄引用的队列包含数据，因此直接从队列中读取数据。它从队列中读取的数据是一个指向字符串的指针，接收任务会将其打印出来。

如果对xQueueSelectFromSet()的调用超时，则它将返回NULL。在示例12中，xQueueSelectFromSet()被调用时具有无限期的阻塞时间，因此永远不会超时，只能返回一个有效的队列句柄。因此，在使用返回值之前，接收任务不需要检查xQueueSelectFromSet()是否返回NULL。

只有当队列句柄引用的队列包含数据时，xQueueSelectFromSet()才会返回队列句柄，因此在从队列中读取数据时不需要使用阻塞时间。

接收任务的实现在清单 65中展示。

---

```c
void vReceiverTask( void *pvParameters )
{
    QueueHandle_t xQueueThatContainsData;
    char *pcReceivedString;
    /* As per most tasks, this task is implemented within an infinite loop. */
    for( ;; )
    {
        /* Block on the queue set to wait for one of the queues in the set to contain data.
        Cast the QueueSetMemberHandle_t value returned from xQueueSelectFromSet() to a
        QueueHandle_t, as it is known all the members of the set are queues (the queue set
        does not contain any semaphores). */
        xQueueThatContainsData = ( QueueHandle_t ) xQueueSelectFromSet( xQueueSet,
        portMAX_DELAY );
        /* An indefinite block time was used when reading from the queue set, so
        xQueueSelectFromSet() will not have returned unless one of the queues in the set
        contained data, and xQueueThatContainsData cannot be NULL. Read from the queue. It
        is not necessary to specify a block time because it is known the queue contains
        data. The block time is set to 0. */
        xQueueReceive( xQueueThatContainsData, &pcReceivedString, 0 );
        /* Print the string received from the queue. */
        vPrintString( pcReceivedString );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单65. 示例12使用的接收任务的实现。</div> </center>

根据示例 12生成的图 37显示的输出结果可以看出，接收任务从两个发送任务接收到了字符串。vSenderTask1()使用的阻塞时间是vSenderTask2()使用的阻塞时间的一半，导致vSenderTask1()发送的字符串被打印的频率是vSenderTask2()发送的字符串的两倍。这说明了不同的发送任务之间可以使用不同的阻塞时间来控制数据发送的频率。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure37.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图37. 执行示例12时生成的输出结果。</div> </center>

#### 更现实的队列集使用情况

示例12演示了一个非常简单的情况；队列集仅包含队列，而它所包含的两个队列都用于发送字符指针。在实际应用中，队列集可能包含队列和信号量，而且队列可能不完全保存相同的数据类型。在这种情况下，在使用xQueueSelectFromSet()返回的值之前，有必要测试它的值。清单 66演示了如何在集合具有以下成员时使用xQueueSelectFromSet()返回的值：

1. 一个二值信号量。

2. 一个用于读取字符指针的队列。

3. 一个用于读取uint32_t值的队列。

Listing 66假设队列和信号量已经被创建并添加到队列集中。

---

```c
/* The handle of the queue from which character pointers are received. */
QueueHandle_t xCharPointerQueue;
/* The handle of the queue from which uint32_t values are received. */
QueueHandle_t xUint32tQueue;
/* The handle of the binary semaphore. */
SemaphoreHandle_t xBinarySemaphore;
/* The queue set to which the two queues and the binary semaphore belong. */
QueueSetHandle_t xQueueSet;
void vAMoreRealisticReceiverTask( void *pvParameters )
{
    QueueSetMemberHandle_t xHandle;
    char *pcReceivedString;
    uint32_t ulRecievedValue;
    const TickType_t xDelay100ms = pdMS_TO_TICKS( 100 );
    for( ;; )
    {
        /* Block on the queue set for a maximum of 100ms to wait for one of the members of
        the set to contain data. */
        xHandle = xQueueSelectFromSet( xQueueSet, xDelay100ms );
        /* Test the value returned from xQueueSelectFromSet(). If the returned value is
        NULL then the call to xQueueSelectFromSet() timed out. If the returned value is not
        NULL then the returned value will be the handle of one of the set’s members. The
        QueueSetMemberHandle_t value can be cast to either a QueueHandle_t or a
        SemaphoreHandle_t. Whether an explicit cast is required depends on the compiler. */
        if( xHandle == NULL )
        {
            /* The call to xQueueSelectFromSet() timed out. */
        }
        else if( xHandle == ( QueueSetMemberHandle_t ) xCharPointerQueue )
        {
            /* The call to xQueueSelectFromSet() returned the handle of the queue that
            receives character pointers. Read from the queue. The queue is known to contain
            data, so a block time of 0 is used. */
            xQueueReceive( xCharPointerQueue, &pcReceivedString, 0 );
            /* The received character pointer can be processed here... */
        }
        else if( xHandle == ( QueueSetMemberHandle_t ) xUint32tQueue )
        {
            /* The call to xQueueSelectFromSet() returned the handle of the queue that
            receives uint32_t types. Read from the queue. The queue is known to contain
            data, so a block time of 0 is used. */
            xQueueReceive(xUint32tQueue, &ulRecievedValue, 0 );
            /* The received value can be processed here... */
        }
        else if( xHandle == ( QueueSetMemberHandle_t ) xBinarySemaphore )
        {
            /* The call to xQueueSelectFromSet() returned the handle of the binary semaphore.
            Take the semaphore now. The semaphore is known to be available so a block time
            of 0 is used. */
            xSemaphoreTake( xBinarySemaphore, 0 );
            /* Whatever processing is necessary when the semaphore is taken can be performed
            here... */
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单66. 使用包含队列和信号量的队列集。</div> </center>

### 使用队列创建邮箱

在嵌入式社区中，术语的使用没有达成共识，而且在不同的实时操作系统中，“邮箱”可能指的是不同的东西。在本书中，术语“邮箱”用来指代长度为一的队列。队列之所以会被描述为邮箱，是因为它在应用程序中的使用方式，而不是因为它在功能上与队列有区别：

- 队列用于从一个任务发送数据到另一个任务，或从中断服务例程发送数据到任务。发送者将一个项目放入队列，接收者从队列中取出该项目。数据通过队列从发送者传递到接收者。
- 邮箱用于保存可以被任何任务或任何中断服务例程读取的数据。数据不通过邮箱传递，而是保留在邮箱中直到被覆写。发送者会覆写邮箱中的值。接收者从邮箱中读取值，但不会从邮箱中移除该值。

本章描述了两个队列API函数，允许队列被用作邮箱。

清单 67展示了创建一个用作邮箱的队列。

---

```c
/* A mailbox can hold a fixed size data item. The size of the data item is set
when the mailbox (queue) is created. In this example the mailbox is created to
hold an Example_t structure. Example_t includes a time stamp to allow the data held in the mailbox to note the time at which the mailbox was last updated. The time stamp used in this example is for demonstration purposes only - a mailbox can hold any data the application writer wants, and the data does not need to include a time stamp. */
typedef struct xExampleStructure
{
    TickType_t xTimeStamp;
    uint32_t ulValue;
} Example_t;
/* A mailbox is a queue, so its handle is stored in a variable of type
QueueHandle_t. */
QueueHandle_t xMailbox;
void vAFunction( void )
{
    /* Create the queue that is going to be used as a mailbox. The queue has a
    length of 1 to allow it to be used with the xQueueOverwrite() API function, which
    is described below. */
    xMailbox = xQueueCreate( 1, sizeof( Example_t ) );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单67. 创建用作邮箱的队列。</div> </center>

#### `xQueueOverwrite()` API函数

与xQueueSendToBack() API函数类似，xQueueOverwrite() API函数将数据发送到队列。与xQueueSendToBack()不同的是，如果队列已满，xQueueOverwrite()将覆盖已经在队列中的数据。 xQueueOverwrite()应仅用于长度为一的队列。这个限制避免了在队列已满的情况下，函数的实现需要做出关于要覆盖队列中的哪个项目的决定。

注意：永远不要从中断服务例程调用xQueueOverwrite()。应该使用中断安全版本的xQueueOverwriteFromISR()。

---

```c
BaseType_t xQueueOverwrite( QueueHandle_t xQueue, const void * pvItemToQueue );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单68. xQueueOverwrite() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     xQueue     | 这是被发送（写入）数据的队列的句柄。队列句柄将会在调用xQueueCreate()创建队列时返回。 |
| pvItemToQueue  |               指向要复制到队列中的数据的指针。               |
|                | 队列可以容纳的每个项目的大小在队列创建时设置，因此将从pvItemToQueue中复制这么多字节到队列存储区。 |
|     返回值     | xQueueOverwrite()会在队列已满的情况下写入队列，因此pdPASS是唯一可能的返回值。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 26. xQueueOverwrite() 参数和返回值。</div> </center>

清单 69展示了如何使用xQueueOverwrite()写入在清单 67中创建的邮箱（队列）。

---

```c
void vUpdateMailbox( uint32_t ulNewValue )
{
    /* Example_t was defined in Listing 67. */
    Example_t xData;
    /* Write the new data into the Example_t structure.*/
    xData.ulValue = ulNewValue;
    /* Use the RTOS tick count as the time stamp stored in the Example_t structure. */
    xData.xTimeStamp = xTaskGetTickCount();
    /* Send the structure to the mailbox - overwriting any data that is already in the
    mailbox. */
    xQueueOverwrite( xMailbox, &xData );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单69. 使用xQueueOverwrite() API 函数。</div> </center>

#### `xQueuePeek()` API函数

xQueuePeek()用于从队列接收（读取）一个项目，而不会将该项目从队列中移除。xQueuePeek()从队列头接收数据，而不会修改队列中存储的数据或数据存储的顺序。

注意：永远不要从中断服务例程调用xQueuePeek()。应该使用中断安全版本的xQueuePeekFromISR()。

xQueuePeek()具有与xQueueReceive()相同的函数参数和返回值。

---

```c
BaseType_t xQueuePeek( QueueHandle_t xQueue,
                       void * const pvBuffer,
                       TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单70. xQueuePeek()) API 函数原型。</div> </center>

清单 71展示了如何使用xQueuePeek()接收在清单 69中发布到邮箱（队列）的项目。

---

```c
BaseType_t vReadMailbox( Example_t *pxData )
{
    TickType_t xPreviousTimeStamp;
    BaseType_t xDataUpdated;
    /* This function updates an Example_t structure with the latest value received
    from the mailbox. Record the time stamp already contained in *pxData before it
    gets overwritten by the new data. */
    xPreviousTimeStamp = pxData->xTimeStamp;
    /* Update the Example_t structure pointed to by pxData with the data contained in
    the mailbox. If xQueueReceive() was used here then the mailbox would be left
    empty, and the data could not then be read by any other tasks. Using
    xQueuePeek() instead of xQueueReceive() ensures the data remains in the mailbox.
    A block time is specified, so the calling task will be placed in the Blocked
    state to wait for the mailbox to contain data should the mailbox be empty. An
    infinite block time is used, so it is not necessary to check the value returned
    from xQueuePeek(), as xQueuePeek() will only return when data is available. */
    xQueuePeek( xMailbox, pxData, portMAX_DELAY );
    /* Return pdTRUE if the value read from the mailbox has been updated since this
    function was last called. Otherwise return pdFALSE. */
    if( pxData->xTimeStamp > xPreviousTimeStamp )
    {
        xDataUpdated = pdTRUE;
    }
    else
    {
        xDataUpdated = pdFALSE;
    }
    return xDataUpdated;
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单71. 使用xQueuePeek()) API 函数。</div> </center>

## 软件定时器管理

### 章节介绍和范围

软件定时器用于在将来的某个特定时间或以固定频率定期执行函数。由软件定时器执行的函数称为软件定时器的回调函数。

软件定时器由FreeRTOS内核实现并受其控制。它们不需要硬件支持，与硬件定时器或硬件计数器无关。

需要注意的是，遵循FreeRTOS使用创新设计以确保最大效率的哲学，软件定时器不会使用任何处理时间，除非实际执行软件定时器回调函数。

软件定时器功能是可选的。要包含软件定时器功能：

1. 将FreeRTOS源文件FreeRTOS/Source/timers.c作为项目的一部分进行构建。
2. 在FreeRTOSConfig.h中将configUSE_TIMERS设置为1。 

#### 范围

本章旨在让读者充分了解：

- 软件定时器的特性与任务特性之间的区别。
- 实时操作系统守护任务。
- 定时器命令队列。
- 单次触发软件定时器和周期性软件定时器之间的区别。
- 如何创建、启动、重置和更改软件定时器的周期。

### 软件定时器回调函数

软件定时器回调函数是以C函数的形式实现的。它们唯一特殊的地方在于它们的原型，它必须返回`void`，并且只接受一个软件定时器的句柄作为参数。回调函数的原型如清单 72所示。

---

```c
void ATimerCallback( TimerHandle_t xTimer );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单72. 软件定时器回调函数原型。</div> </center>

软件定时器回调函数从开始执行到结束，并以正常方式退出。它们应该保持简短，不能进入阻塞状态。

注意：正如将要看到的，软件定时器回调函数在启动FreeRTOS调度器时自动创建的任务上下文中执行。因此，软件定时器回调函数绝不能调用会导致调用任务进入阻塞状态的FreeRTOS API函数。可以调用像xQueueReceive()这样的函数，但前提是该函数的xTicksToWait参数（指定函数的阻塞时间）设置为0。不可以调用vTaskDelay()等函数，因为调用vTaskDelay()将导致调用任务始终置于阻塞状态。

### 软件定时器的属性和状态

#### 软件定时器的周期

软件定时器的“周期”是指软件定时器启动和软件定时器的回调函数执行之间的时间间隔。

#### 单次触发定时器和自动重载定时器

有两种类型的软件定时器：

1. 单次触发定时器

   一旦启动，单次触发定时器只会执行其回调函数一次。单次触发定时器可以手动重新启动，但不会自行重新启动。

2. 自动重载定时器

   一旦启动，自动重载定时器会在每次到期时重新启动自身，从而导致其回调函数的周期性执行。 


图 38显示了一次触发定时器和自动重载定时器之间行为的差异。虚线垂直线标记了时钟中断发生的时间。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure38.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图38. 单次触发和自动重载软件定时器之间的行为差异。</div> </center>

参考图38：

- 定时器1

  定时器1是一个单次触发定时器，其周期为6个时钟滴答。它在时间t1启动，因此其回调函数在6个时钟滴答后的t7时间执行。由于定时器1是单次触发定时器，它的回调函数不会再次执行。

- 定时器2

  定时器2是一个自动重载定时器，其周期为5个时钟滴答。它在时间t1启动，因此其回调函数在时间t1后每隔5个时钟滴答执行一次。在图38中，这发生在t6、t11和t16时。

#### 软件定时器状态

一个软件定时器可以处于以下两种状态之一：

- 休眠状态（Dormant）

  休眠状态的软件定时器存在，并且可以通过其句柄引用，但它没有在运行，因此其回调函数不会执行。

- 运行状态（Running）

  运行状态的软件定时器将在进入运行状态或上次重置后的时间等于其周期的时间之后执行其回调函数。

图 39和图 40分别显示了自动重载定时器和单次触发定时器在休眠状态和运行状态之间可能发生的状态转换。两个图表之间的主要区别是定时器到期后进入的状态；自动重载定时器执行其回调函数然后重新进入运行状态，而单次触发定时器执行其回调函数然后进入休眠状态。

xTimerDelete() API函数用于删除定时器，定时器可以在任何时候删除。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure39.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图39. 自动重载软件定时器的状态和转换。</div> </center>

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure40.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图40. 单次触发软件定时器的状态和转换。</div> </center>

### 软件定时器的上下文

#### RTOS守护任务（定时器服务任务）

所有软件定时器回调函数都在同一个RTOS守护任务（或称为“定时器服务”任务）的上下文中执行[^9]。

守护任务是一个标准的FreeRTOS任务，在调度器启动时会自动创建。它的优先级和堆栈大小由编译时配置常量`configTIMER_TASK_PRIORITY`和`configTIMER_TASK_STACK_DEPTH`分别设置。这两个常量在FreeRTOSConfig.h中定义。

软件定时器回调函数不得调用导致任务进入阻塞状态的FreeRTOS API函数，因为这样做将导致守护任务进入阻塞状态。

[^9]:该任务以前被称为“定时器服务任务”，因为最初它仅用于执行软件定时器回调函数。现在，同一个任务也用于其他用途，因此它被更通用的名称“RTOS守护任务”所称呼。

#### 定时器命令队列

软件定时器API函数将命令从调用任务发送到守护任务的队列，称为“定时器命令队列”。如图 41所示，命令的示例包括“启动定时器”、“停止定时器”和“重置定时器”。

定时器命令队列是一个标准的FreeRTOS队列，在调度器启动时会自动创建。定时器命令队列的长度由FreeRTOSConfig.h中的`configTIMER_QUEUE_LENGTH`编译时配置常量设置。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure41.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图41. 定时器命令队列用于软件定时器API函数与RTOS守护任务之间进行通信。</div> </center>

#### 守护任务调度

守护任务的调度方式与任何其他FreeRTOS任务一样；只有当它是能够运行的最高优先级任务时，它才会处理命令或执行定时器回调函数。图 42和图 43演示了`configTIMER_TASK_PRIORITY`设置如何影响其执行模式。

图 42展示了当守护任务的优先级低于调用xTimerStart() API函数的任务优先级时的执行模式。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure42.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图42. 调用xTimerStart()的任务的优先级高于守护任务的优先级时的执行模式。</div> </center>

参考图 42，其中任务1的优先级高于守护任务的优先级，守护任务的优先级高于空闲任务的优先级：

1. 在时间t1：

   - 任务1处于运行状态，守护任务处于阻塞状态。
   - 如果向定时器命令队列发送了命令，守护任务将离开阻塞状态并处理该命令。
   - 如果软件定时器到期，守护任务将执行软件定时器的回调函数。

2. 在时间t2：

   - 任务1调用xTimerStart()。
   - xTimerStart()向定时器命令队列发送一个命令，导致守护任务离开阻塞状态。
   - 由于任务1的优先级高于守护任务，守护任务不会抢占任务1。
   - 任务1仍处于运行状态，而守护任务已离开阻塞状态并进入就绪状态。

3. 在时间t3：

   - 任务1完成执行xTimerStart() API函数。任务1从函数的开始到结束执行xTimerStart()，没有离开运行状态。

4. 在时间t4：

   - 任务1调用导致它进入阻塞状态的API函数。

   - 守护任务现在是就绪状态中优先级最高的任务，所以调度器选择守护任务作为进入运行状态的任务。

   - 守护任务随后开始处理由任务1发送到定时器命令队列的命令。

     注意：软件定时器的启动到期时间是根据“启动定时器”命令发送到定时器命令队列的时间计算的，而不是根据守护任务从定时器命令队列接收“启动定时器”命令的时间计算的。

5. 在时间t5：

   - 守护任务已经完成处理由任务1发送的命令，并尝试从定时器命令队列中接收更多数据。

   - 定时器命令队列为空，因此守护任务重新进入阻塞状态。

   - 如果向定时器命令队列发送了命令，守护任务将再次离开阻塞状态，或者如果软件定时器到期，也会离开阻塞状态。

   - 此时，空闲任务是就绪状态中优先级最高的任务，所以调度器选择空闲任务作为进入运行状态的任务。

图 43展示了与图 42类似的情况，但这次守护任务的优先级高于调用xTimerStart()的任务的优先级。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure43.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图43. 调用xTimerStart()的任务的优先级低于守护任务的优先级时的执行模式。</div> </center>

参考图 43，在这种情况下，守护任务的优先级高于任务1的优先级，任务1的优先级高于空闲任务的优先级：

1. 在时间t1：
   - 与之前一样，任务1处于运行状态，守护任务处于阻塞状态。
2. 在时间t2：
   - 任务1调用xTimerStart()。
   - xTimerStart()向定时器命令队列发送一个命令，导致守护任务离开阻塞状态。
   - 守护任务的优先级高于任务1的优先级，因此调度器选择守护任务作为进入运行状态的任务。
   - 任务1在执行完xTimerStart()函数之前被守护任务抢占，并且现在处于就绪状态。
   - 守护任务开始处理由任务1发送到定时器命令队列的命令。
3. 在时间t3：
   - 守护任务已经完成处理由任务1发送的命令，并尝试从定时器命令队列中接收更多数据。
   - 定时器命令队列为空，因此守护任务重新进入阻塞状态。
   - 此时，任务1是就绪状态中优先级最高的任务，所以调度器选择任务1作为进入运行状态的任务。
4. 在时间t4：
   - 任务1在执行xTimerStart()函数之前被守护任务抢占，并且只有在重新进入运行状态后才退出（从xTimerStart()函数中返回）。
5. 在时间t5：
   - 任务1调用导致它进入阻塞状态的API函数。
   - 现在，空闲任务是就绪状态中优先级最高的任务，所以调度器选择空闲任务作为进入运行状态的任务。

在图 42所示的情景中，任务1发送命令到定时器命令队列后，经过一段时间，守护任务才接收和处理该命令。而在图43 所示的情景中，守护任务在任务1发送命令后已经接收并处理了该命令，然后任务1才从发送命令的函数中返回。

发送到定时器命令队列的命令包含一个时间戳。时间戳用于考虑应用任务发送命令和守护任务处理命令之间经过的时间。例如，如果发送一个“启动定时器”的命令来启动一个周期为10个时钟节拍的定时器，时间戳将用于确保被启动的定时器在命令被发送后的10个时钟节拍后到期，而不是在守护任务处理命令后的10个时钟节拍后到期。

### 创建和启动一个软件定时器

#### `xTimerCreate()` API函数

一个软件定时器必须在使用之前明确地创建。

软件定时器通过类型为 TimerHandle_t 的变量进行引用。使用 xTimerCreate() 函数创建软件定时器，并返回一个 TimerHandle_t 以引用所创建的软件定时器。软件定时器被创建时处于休眠状态。

软件定时器可以在调度器运行之前创建，也可以在调度器启动后从任务中创建。 

---

```c
TimerHandle_t xTimerCreate( const char * const pcTimerName,
                            TickType_t xTimerPeriodInTicks,
                            UBaseType_t uxAutoReload,
                            void * pvTimerID,
                            TimerCallbackFunction_t pxCallbackFunction );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单73. xTimerCreate() API 函数原型。</div> </center>

|   参数名/ 返回值    |                             描述                             |
| :-----------------: | :----------------------------------------------------------: |
|     pcTimerName     | 定时器的描述性名称。FreeRTOS 不以任何方式使用它，它仅作为调试辅助工具包含在内。通过人类可读的名称来识别定时器要比尝试通过其句柄来识别它简单得多。 |
| xTimerPeriodInTicks | 以时钟节拍为单位指定的定时器周期。可以使用 pdMS_TO_TICKS() 宏将以毫秒指定的时间转换为以节拍为单位的时间。 |
|    uxAutoReload     | 将 uxAutoReload 设置为 pdTRUE 可以创建一个自动重载定时器。将 uxAutoReload 设置为 pdFALSE 可以创建一个单次触发定时器。 |
|      pvTimerID      | 每个软件定时器都有一个ID值。该ID是一个`void`指针，应用程序编写者可以用于任何目的。当同一个回调函数被多个软件定时器使用时，ID特别有用，因为它可以用于提供定时器特定的存储空间。在本章中的一个示例中演示了如何使用定时器的ID。 |
|                     |      `pvTimerID` 用于设置正在创建的定时器的ID的初始值。      |
| pxCallbackFunction  | 软件定时器的回调函数只能是符合清单 72 中所示原型的 C 函数。`pxCallbackFunction` 参数是一个指向用作正在创建的软件定时器的回调函数的函数的指针（实际上就是函数名称）。 |
|       返回值        | 如果返回 NULL，则表示无法创建软件定时器，因为没有足够的堆内存可用于 FreeRTOS 分配所需的数据结构。 |
|                     | 返回非 NULL 值表示已成功创建软件定时器。返回的值是已创建定时器的句柄。 |
|                     |            第2章提供了有关堆内存管理的更多信息。             |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 27. xTimerCreate() 参数和返回值。</div> </center>

#### `xTimerStart()` API 函数

xTimerStart() 用于启动处于休眠状态的软件定时器，或者重置（重新启动）处于运行状态的软件定时器。xTimerStop() 用于停止运行状态的软件定时器。停止软件定时器等同于将定时器转换为休眠状态。

 xTimerStart() 可以在调度程序启动之前调用，但这样做时，软件定时器实际上不会在调度程序启动的时间之前启动。

注意：绝不要从中断服务例程中调用 xTimerStart()。应使用中断安全版本 xTimerStartFromISR() 来代替。

---

```c
BaseType_t xTimerStart( TimerHandle_t xTimer, TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单74. xTimerStart() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     xTimer     | 正在启动或重置的软件定时器的句柄。该句柄将是通过调用用于创建软件定时器的 `xTimerCreate()` 返回的。 |
|  xTicksToWait  | `xTimerStart()` 使用定时器命令队列向守护任务发送“启动定时器”命令。如果队列已满的话,`xTicksToWait` 指定了调用任务应该在阻塞状态等待定时器命令队列中空闲空间的最长时间。 |
|                | 如果 `xTicksToWait` 为零且定时器命令队列已满，`xTimerStart()` 将立即返回。 |
|                | 阻塞时间以时钟节拍为单位指定，因此它表示的绝对时间取决于时钟节拍频率。宏 `pdMS_TO_TICKS()` 可用于将以毫秒指定的时间转换为以时钟节拍为单位的时间。 |
|                | 如果在 FreeRTOSConfig.h 中将 `INCLUDE_vTaskSuspend` 设置为 1，那么将 `xTicksToWait` 设置为 `portMAX_DELAY` 将导致调用任务无限期地保持在阻塞状态（无超时）以等待定时器命令队列中的可用空间。 |
|                | 如果在调度程序启动之前调用 `xTimerStart()`，将忽略 `xTicksToWait` 的值，`xTimerStart()` 的行为就像 `xTicksToWait` 已设置为零一样。 |
|     返回值     |                     有两种可能的返回值：                     |
|                | 1. pdPASS 只有当成功发送“启动定时器”命令到定时器命令队列时，才会返回pdPASS。 如果守护任务的优先级高于调用xTimerStart()的任务的优先级，调度程序会确保在xTimerStart()返回之前处理启动命令。这是因为一旦定时器命令队列中有数据，守护任务将抢占调用xTimerStart()的任务。 如果指定了阻塞时间（xTicksToWait不为零），则在函数返回之前，可能会将调用任务置于阻塞状态，等待定时器命令队列中的空间变得可用，但在阻塞时间到期之前，数据可能已成功写入定时器命令队列。 |
|                | 2. pdFALSE 如果无法将“启动定时器”命令写入定时器命令队列，因为队列已满，将返回pdFALSE。 如果指定了阻塞时间（xTicksToWait不为零），则调用任务将被置于阻塞状态，等待守护任务在定时器命令队列中腾出空间，但在此之前可能指定的阻塞时间已过期。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 28. xTimerStart() 参数和返回值。</div> </center>

#### 示例13. 创建单次触发和自动重载定时器 

本示例创建并启动一个单次触发的定时器和一个自动重载的定时器，如清单75所示。

---

```c
/* The periods assigned to the one-shot and auto-reload timers are 3.333 second and half a second respectively. */
#define mainONE_SHOT_TIMER_PERIOD pdMS_TO_TICKS( 3333 )
#define mainAUTO_RELOAD_TIMER_PERIOD pdMS_TO_TICKS( 500 )
int main( void )
{
    TimerHandle_t xAutoReloadTimer, xOneShotTimer;
    BaseType_t xTimer1Started, xTimer2Started;
    /* Create the one shot timer, storing the handle to the created timer in xOneShotTimer. */
    xOneShotTimer = xTimerCreate(
                    /* Text name for the software timer - not used by FreeRTOS. */
                    "OneShot",
                    /* The software timer's period in ticks. */
                    mainONE_SHOT_TIMER_PERIOD,
                    /* Setting uxAutoRealod to pdFALSE creates a one-shot software timer. */
                    pdFALSE,
                    /* This example does not use the timer id. */
                    0,
                    /* The callback function to be used by the software timer being created. */
                    prvOneShotTimerCallback );
    /* Create the auto-reload timer, storing the handle to the created timer in xAutoReloadTimer. */
    xAutoReloadTimer = xTimerCreate(
                    /* Text name for the software timer - not used by FreeRTOS. */
                    "AutoReload",
                    /* The software timer's period in ticks. */
                    mainAUTO_RELOAD_TIMER_PERIOD,
                    /* Setting uxAutoRealod to pdTRUE creates an auto-reload timer. */
                    pdTRUE,
                    /* This example does not use the timer id. */
                    0,
                    /* The callback function to be used by the software timer being created. */
                    prvAutoReloadTimerCallback );
    /* Check the software timers were created. */
    if( ( xOneShotTimer != NULL ) && ( xAutoReloadTimer != NULL ) )
    {
        /* Start the software timers, using a block time of 0 (no block time). The scheduler has
        not been started yet so any block time specified here would be ignored anyway. */
        xTimer1Started = xTimerStart( xOneShotTimer, 0 );
        xTimer2Started = xTimerStart( xAutoReloadTimer, 0 );
        /* The implementation of xTimerStart() uses the timer command queue, and xTimerStart()
        will fail if the timer command queue gets full. The timer service task does not get
        created until the scheduler is started, so all commands sent to the command queue will
        stay in the queue until after the scheduler has been started. Check both calls to
        xTimerStart() passed. */
        if( ( xTimer1Started == pdPASS ) && ( xTimer2Started == pdPASS ) )
        {
            /* Start the scheduler. */
            vTaskStartScheduler();
        }
    }
    /* As always, this line should not be reached. */
    for( ;; );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单75. 创建并启动示例13中使用的定时器。</div> </center>

定时器的回调函数在每次调用时只打印一条消息。单次触发定时器回调函数的实现如清单76所示，自动重载定时器回调函数的实现如清单77所示。

---

```c
static void prvOneShotTimerCallback( TimerHandle_t xTimer )
{
    TickType_t xTimeNow;
    /* Obtain the current tick count. */
    xTimeNow = xTaskGetTickCount();
    /* Output a string to show the time at which the callback was executed. */
    vPrintStringAndNumber( "One-shot timer callback executing", xTimeNow );
    /* File scope variable. */
    ulCallCount++;
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单76. 示例13中单次触发定时器使用的回调函数。</div> </center>

---

```c
static void prvAutoReloadTimerCallback( TimerHandle_t xTimer )
{
    TickType_t xTimeNow;
    /* Obtain the current tick count. */
    xTimeNow = uxTaskGetTickCount();
    /* Output a string to show the time at which the callback was executed. */
    vPrintStringAndNumber( "Auto-reload timer callback executing", xTimeNow );
    ulCallCount++;
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单77. 示例13中自动重载定时器使用的回调函数。</div> </center>

执行此示例将产生如图 44所示的输出。图 44显示了自动重载定时器的回调函数以固定的500个时钟节拍为周期执行（在清单75中将mainAUTO_RELOAD_TIMER_PERIOD设置为500），以及单次触发定时器的回调函数仅在时钟节拍计数为3333时执行一次（在清单75中将mainONE_SHOT_TIMER_PERIOD设置为3333）。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure44.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图44. 执行示例13时产生的输出。</div> </center>

### 定时器标识

每个软件定时器都有一个ID，这是一个标签值，应用程序编写者可以用于任何目的。ID存储在一个void指针（void *）中，因此可以直接存储整数值，指向任何其他对象，或者用作函数指针。

在创建软件定时器时会为ID分配一个初始值，然后可以使用vTimerSetTimerID() API函数来更新ID，使用pvTimerGetTimerID() API函数来查询ID。

与其他软件定时器API函数不同，vTimerSetTimerID()和pvTimerGetTimerID() 直接访问软件定时器，它们不会向定时器命令队列发送命令。

#### `vTimerSetTimerID()` API函数

---

```c
void vTimerSetTimerID( const TimerHandle_t xTimer, void *pvNewID );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单78. vTimerSetTimerID() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     xTimer     | 被更新的软件定时器的句柄，此句柄从调用xTimerCreate()创建该软件定时器时返回。 |
|    pvNewID     |                 将要设置为软件定时器ID的值。                 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 29. vTimerSetTimerID() 参数。</div> </center>

#### `pvTimerGetTimerID()` API函数

---

```c
void *pvTimerGetTimerID( TimerHandle_t xTimer );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单79. pvTimerGetTimerID() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     xTimer     | 要查询的软件定时器的句柄。该句柄将会从调用xTimerCreate()创建软件定时器时返回。 |
|     返回值     |                   要查询的软件定时器的ID。                   |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 30. pvTimerGetTimerID() 参数和返回值。</div> </center>

#### 示例14. 使用回调函数参数和软件定时器ID

相同的回调函数可以分配给多个软件定时器。当这样做时，回调函数参数用于确定哪个软件定时器已经到期。 

示例13使用了两个不同的回调函数；一个回调函数被用于单次触发定时器，另一个回调函数被用于自动重载定时器。示例14创建了与示例13相似的功能，但将一个回调函数分配给了两个软件定时器。 

示例14中使用的main()函数几乎与示例13中使用的main()函数相同。唯一的区别在于软件定时器的创建位置。这个区别在清单80中显示，其中prvTimerCallback()被用作两个定时器的回调函数。

---

```c
/* Create the one shot timer software timer, storing the handle in xOneShotTimer. */
xOneShotTimer = xTimerCreate( "OneShot",
                             mainONE_SHOT_TIMER_PERIOD,
                             pdFALSE,
                             /* The timer’s ID is initialized to 0. */
                             0,
                             /* prvTimerCallback() is used by both timers. */
                             prvTimerCallback );
/* Create the auto-reload software timer, storing the handle in xAutoReloadTimer */
xAutoReloadTimer = xTimerCreate( "AutoReload",
                                 mainAUTO_RELOAD_TIMER_PERIOD,
                                 pdTRUE,
                                 /* The timer’s ID is initialized to 0. */
                                 0,
                                 /* prvTimerCallback() is used by both timers. */
                                 prvTimerCallback );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单80. 创建示例14中使用的定时器。</div> </center>

prvTimerCallback() 在任一定时器到期时都会执行。prvTimerCallback() 的实现使用函数的参数来确定是因为单次触发定时器到期而被调用，还是因为自动重载定时器到期而被调用。

prvTimerCallback() 还演示了如何使用软件定时器ID作为定时器特定的存储方式；每个软件定时器在其自己的ID中保留了它已经到期的次数，并且自动重载定时器使用这个计数来在第五次执行时停止自己。

prvTimerCallback() 的实现如清单81所示。

---

```c
static void prvTimerCallback( TimerHandle_t xTimer )
{
    TickType_t xTimeNow;
    uint32_t ulExecutionCount;
    /* A count of the number of times this software timer has expired is stored in the timer's
    ID. Obtain the ID, increment it, then save it as the new ID value. The ID is a void
    pointer, so is cast to a uint32_t. */
    ulExecutionCount = ( uint32_t ) pvTimerGetTimerID( xTimer );
    ulExecutionCount++;
    vTimerSetTimerID( xTimer, ( void * ) ulExecutionCount );
    /* Obtain the current tick count. */
    xTimeNow = xTaskGetTickCount();
    /* The handle of the one-shot timer was stored in xOneShotTimer when the timer was created.
    Compare the handle passed into this function with xOneShotTimer to determine if it was the
    one-shot or auto-reload timer that expired, then output a string to show the time at which
    the callback was executed. */
    if( xTimer == xOneShotTimer )
    {
        vPrintStringAndNumber( "One-shot timer callback executing", xTimeNow );
    }
    else
    {
        /* xTimer did not equal xOneShotTimer, so it must have been the auto-reload timer that
        expired. */
        vPrintStringAndNumber( "Auto-reload timer callback executing", xTimeNow );
        if( ulExecutionCount == 5 )
        {
            /* Stop the auto-reload timer after it has executed 5 times. This callback function
            executes in the context of the RTOS daemon task so must not call any functions that
            might place the daemon task into the Blocked state. Therefore a block time of 0 is
            used. */
            xTimerStop( xTimer, 0 );
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单81. 示例14中使用的定时器回调函数。</div> </center>

示例14生成的输出如图45所示。可以看到自动重载定时器只执行了五次。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure45.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图45. 执行示例14时产生的输出。</div> </center>

### 改变定时器的周期

每个官方的FreeRTOS端口都配备了一个或多个示例项目。大多数示例项目都具有自我检查功能，使用LED来提供项目状态的可视反馈；如果自我检查一直通过，则LED会以慢速切换，如果自我检查曾经失败，则LED会快速切换。

一些示例项目在任务中执行自我检查，并使用vTaskDelay()函数来控制LED切换的速率。其他示例项目则在软件定时器的回调函数中执行自我检查，并使用定时器的周期来控制LED切换的速率。

#### `xTimerChangePeriod()` API 函数

使用xTimerChangePeriod()函数来改变软件定时器的周期。

如果xTimerChangePeriod()被用于改变已经运行的定时器的周期，那么定时器将使用新的周期值来重新计算其到期时间。重新计算的到期时间是相对于调用xTimerChangePeriod()时的时间，而不是相对于定时器最初启动的时间。

如果xTimerChangePeriod()被用于改变处于休眠状态（即未运行）的定时器的周期，那么定时器将计算一个到期时间，并在这个时间到期之后切换到运行状态（定时器将开始运行）。

注意：绝不要从中断服务例程中调用xTimerChangePeriod()。应该使用中断安全版本的xTimerChangePeriodFromISR()来代替。

---

```c
BaseType_t xTimerChangePeriod( TimerHandle_t xTimer,
                               TickType_t xNewTimerPeriodInTicks,
                               TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单82. xTimerChangePeriod() API 函数原型。</div> </center>

|   参数名/ 返回值    |                             描述                             |
| :-----------------: | :----------------------------------------------------------: |
|       xTimer        | 要更新周期值的软件定时器的句柄，该句柄将从调用`xTimerCreate()`以创建该软件定时器时返回。 |
| xTimerPeriodInTicks | 软件定时器的新周期，以时钟滴答数（ticks）来指定。可以使用`pdMS_TO_TICKS()`宏将以毫秒指定的时间转换为以滴答数（ticks）指定的时间。 |
|    xTicksToWait     | `xTimerChangePeriod()`使用定时器命令队列将“更改周期”命令发送给守护任务。如果队列已满的话，`xTicksToWait`指定了调用任务应该保持在阻塞状态等待定时器命令队列上的空间可用的最大时间。 |
|                     | 如果`xTicksToWait`为零且定时器命令队列已满，`xTimerChangePeriod()`将立即返回。 |
|                     | 宏`pdMS_TO_TICKS()`可用于将以毫秒指定的时间转换为以滴答数（ticks）指定的时间。 |
|                     | 如果在FreeRTOSConfig.h中将`INCLUDE_vTaskSuspend`设置为1，则将`xTicksToWait`设置为`portMAX_DELAY`将导致调用任务无限期地保持在阻塞状态（没有超时）等待定时器命令队列上的空间可用。 |
|                     | 如果在调度程序启动之前调用了`xTimerChangePeriod()`，则将忽略`xTicksToWait`的值，`xTimerChangePeriod()`会表现得好像`xTicksToWait`已设置为零。 |
|       返回值        |                     有两种可能的返回值：                     |
|                     | 1. pdPASS 只有当数据成功发送到定时器命令队列时，才会返回pdPASS。 如果指定了阻塞时间（xTicksToWait不为零），则可能会将调用任务置于阻塞状态，等待在函数返回之前定时器命令队列中的空间变得可用，但数据在阻塞时间到期之前已成功写入定时器命令队列。 |
|                     | 2. pdFALSE 如果无法将“更改周期”命令写入定时器命令队列，因为队列已经满了，将返回pdFALSE。 如果指定了阻塞时间（xTicksToWait不为零），则调用任务将被置于阻塞状态，等待守护任务在队列中腾出空间，但在指定的阻塞时间到期之前可能队列依旧为满。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 31. xTimerChangePeriod() 参数和返回值。</div> </center>

清单83显示了在软件定时器回调函数中包含自检功能的FreeRTOS示例如何使用xTimerChangePeriod()来增加LED切换速率，如果自检失败。执行自检的软件定时器被称为“检查定时器”。

---

```c
/* The check timer is created with a period of 3000 milliseconds, resulting in the LED toggling every 3 seconds. If the self-checking functionality detects an unexpected state, then the check timer’s period is changed to just 200 milliseconds, resulting in a much faster toggle rate. */
const TickType_t xHealthyTimerPeriod = pdMS_TO_TICKS( 3000 );
const TickType_t xErrorTimerPeriod = pdMS_TO_TICKS( 200 );
/* The callback function used by the check timer. */
static void prvCheckTimerCallbackFunction( TimerHandle_t xTimer )
{
    static BaseType_t xErrorDetected = pdFALSE;
    if( xErrorDetected == pdFALSE )
    {
        /* No errors have yet been detected. Run the self-checking function again. The
        function asks each task created by the example to report its own status, and also checks
        that all the tasks are actually still running (and so able to report their status
        correctly). */
        if( CheckTasksAreRunningWithoutError() == pdFAIL )
        {
            /* One or more tasks reported an unexpected status. An error might have occurred.
            Reduce the check timer’s period to increase the rate at which this callback function
            executes, and in so doing also increase the rate at which the LED is toggled. This
            callback function is executing in the context of the RTOS daemon task, so a block
            time of 0 is used to ensure the Daemon task never enters the Blocked state. */
            xTimerChangePeriod( xTimer, /* The timer being updated. */
                                xErrorTimerPeriod, /* The new period for the timer. */
                                0 ); /* Do not block when sending this command. */
        }
        /* Latch that an error has already been detected. */
        xErrorDetected = pdTRUE;
    }
    /* Toggle the LED. The rate at which the LED toggles will depend on how often this function
    is called, which is determined by the period of the check timer. The timer’s period will
    have been reduced from 3000ms to just 200ms if CheckTasksAreRunningWithoutError() has ever
    returned pdFAIL. */
    ToggleLED();
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单83. 使用 xTimerChangePeriod()。</div> </center>

### 重置软件定时器

重置软件定时器意味着重新启动定时器；定时器的到期时间会被重新计算，相对于定时器被重置的时间，而不是最初启动的时间。这可以通过图 46来演示，图中显示一个周期为6的定时器首先启动，然后重置两次，最终到期并执行其回调函数。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure46.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图46. 启动和重置周期为6滴答的软件定时器。</div> </center>

参考图46：

- 定时器1在时间t1启动。它的周期是6，因此最初计算出它将执行其回调函数的时间是t7，即它启动后的6个滴答。
- 在达到时间t7之前，即在定时器1到期并执行其回调函数之前，定时器1被重置。在时间t5时，定时器1被重置，因此重新计算它将执行其回调函数的时间为t11，即它被重置后的6个滴答。
- 定时器1在达到时间t11之前再次被重置，即在定时器1到期并执行其回调函数之前。在时间t9时，定时器1被重置，因此重新计算它将执行其回调函数的时间为t15，即它最后一次被重置后的6个滴答。
- 定时器1不再被重置，因此它在时间t15到期，并相应地执行其回调函数。

#### `xTimerReset()` API 函数

可以使用xTimerReset()API函数来重置一个定时器。xTimerReset()还可以用于启动处于休眠状态的定时器。

注意：绝不要从中断服务例程中调用xTimerReset()。应该使用中断安全版本的xTimerResetFromISR()来代替。

---

```c
BaseType_t xTimerReset( TimerHandle_t xTimer, TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单84. xTimerReset() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     xTimer     | 要重置或启动的软件定时器的句柄，该句柄将从调用`xTimerCreate()`以创建该软件定时器时返回。 |
|  xTicksToWait  | `xTimerReset()`使用定时器命令队列将“重置”命令发送给守护任务。如果队列已满的话，`xTicksToWait`指定了调用任务应该保持在阻塞状态等待定时器命令队列上的空间变得可用的最大时间。 |
|                | 如果`xTicksToWait`为零且定时器命令队列已满，`xTimerReset()`将立即返回。 |
|                | 如果在FreeRTOSConfig.h中将`INCLUDE_vTaskSuspend`设置为1，则将`xTicksToWait`设置为`portMAX_DELAY`将导致调用任务无限期地保持在阻塞状态（没有超时）等待定时器命令队列上的空间可用。 |
|     返回值     |                     有两种可能的返回值：                     |
|                | 1. pdPASS 只有当数据成功发送到定时器命令队列时，才会返回pdPASS。 如果指定了阻塞时间（xTicksToWait不为零），则可能会将调用任务置于阻塞状态，等待在函数返回之前定时器命令队列中的空间变得可用，但数据在阻塞时间到期之前已成功写入定时器命令队列。 |
|                | 2. pdFALSE 如果无法将“重置”命令写入定时器命令队列，因为队列已经满了，将返回pdFALSE。 如果指定了阻塞时间（xTicksToWait不为零），则调用任务将被置于阻塞状态，等待守护任务在队列中腾出空间，但直至在指定的阻塞时间到期之前队列依旧为满。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 32. xTimerChangePeriod() 参数和返回值。</div> </center>

#### 示例15. 重置软件定时器

这个示例模拟了手机背光的行为。背光：

- 在按下键时打开。
- 在一定时间内保持开启，只要在此期间内再次按下键。
- 如果在一定时间内没有按键，则会自动关闭。 

为了实现这种行为，使用了一个单次触发的软件定时器：

* 当按下键时，（模拟的）背光被打开，并在软件定时器的回调函数中被关闭。
* 每次按下键时都会重置软件定时器。
* 为了防止背光关闭，必须使按键下一次按下的时间段小于等于软件定时器的周期；如果软件定时器在计时器到期之前没有被按键重置，那么计时器的回调函数会执行，并且背光会关闭。

变量xSimulatedBacklightOn保存了背光的状态。xSimulatedBacklightOn被设置为pdTRUE以表示背光是开启的，被设置为pdFALSE以表示背光是关闭的。

软件定时器的回调函数如清单85所示。

---

```c
static void prvBacklightTimerCallback( TimerHandle_t xTimer )
{
    TickType_t xTimeNow = xTaskGetTickCount();
    /* The backlight timer expired, turn the backlight off. */
    xSimulatedBacklightOn = pdFALSE;
    /* Print the time at which the backlight was turned off. */
    vPrintStringAndNumber(
    "Timer expired, turning backlight OFF at time\t\t", xTimeNow );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单85. 示例15中使用的单次触发定时器的回调函数。</div> </center>

示例15创建了一个用于轮询键盘的任务[^10]。任务如清单86所示，但由于下一段中描述的原因，清单86并不代表最佳设计。

使用FreeRTOS允许您的应用程序运用事件驱动设计。事件驱动设计非常高效地使用处理时间，因为只有在事件发生时才使用处理时间，不会浪费处理时间轮询未发生的事件。示例15不能成为事件驱动的，因为在使用FreeRTOS Windows端口时处理键盘中断是不实际的，所以必须使用效率低得多的轮询技术。如果清单86是中断服务例程，那么会使用`xTimerResetFromISR()`来代替`xTimerReset()`。

[^10]:在Windows控制台上打印和读取键盘都会导致执行Windows系统调用。Windows系统调用，包括使用Windows控制台、磁盘或TCP/IP堆栈，可能会对FreeRTOS Windows端口的行为产生不利影响，通常应该避免使用。

---

```c
static void vKeyHitTask( void *pvParameters )
{
    const TickType_t xShortDelay = pdMS_TO_TICKS( 50 );
    TickType_t xTimeNow;
    vPrintString( "Press a key to turn the backlight on.\r\n" );
    /* Ideally an application would be event driven, and use an interrupt to process key
    presses. It is not practical to use keyboard interrupts when using the FreeRTOS Windows
    port, so this task is used to poll for a key press. */
    for( ;; )
    {
        /* Has a key been pressed? */
        if( _kbhit() != 0 )
        {
            /* A key has been pressed. Record the time. */
            xTimeNow = xTaskGetTickCount();
            if( xSimulatedBacklightOn == pdFALSE )
            {
                /* The backlight was off, so turn it on and print the time at which it was
                turned on. */
                xSimulatedBacklightOn = pdTRUE;
                vPrintStringAndNumber(
                "Key pressed, turning backlight ON at time\t\t", xTimeNow );
            }
            else
            {
                /* The backlight was already on, so print a message to say the timer is about to
                be reset and the time at which it was reset. */
                vPrintStringAndNumber(
                "Key pressed, resetting software timer at time\t\t", xTimeNow );
            }
            /* Reset the software timer. If the backlight was previously off, then this call
            will start the timer. If the backlight was previously on, then this call will
            restart the timer. A real application may read key presses in an interrupt. If
            this function was an interrupt service routine then xTimerResetFromISR() must be
            used instead of xTimerReset(). */
            xTimerReset( xBacklightTimer, xShortDelay );
            /* Read and discard the key that was pressed – it is not required by this simple
            example. */
            ( void ) _getch();
        }
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单86. 示例15中用于重置软件定时器的任务。</div> </center>

执行示例15时产生的输出如图 47所示。关于图 47：

- 第一次按键发生在滴答计数为812时。那时背光被打开，并且单次触发定时器被启动。
- 随后的按键分别在滴答计数为1813、3114、4015和5016时发生。所有这些按键导致定时器在到期之前被重置。
- 定时器在滴答计数达到10016时到期。那时背光被关闭。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure47.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图47. 执行示例15时产生的输出。</div> </center>

从图 47可以看出，定时器的周期是5000个滴答；背光在上次按键后恰好5000个滴答后关闭，也就是在上次重置定时器后的5000个滴答后关闭。

## 中断管理

### 章节介绍与范围

#### 事件

嵌入式实时系统必须对来自环境的事件做出响应。例如，以太网外设上到达的数据包（事件）可能需要传递给TCP/IP堆栈进行处理（动作）。复杂的系统将需要处理来自多个源的事件，所有这些源都将具有不同的处理开销和响应时间要求。在每种情况下，必须对最佳事件处理实现策略进行判断：

1. 事件应该如何检测？通常使用中断，但也可以轮询输入。
2. 当使用中断时，在中断服务例程（ISR）内部应该执行多少处理，以及在外部应该执行多少处理？通常希望将每个ISR保持尽可能短。
3. 事件如何与主要（非ISR）代码进行通信，以及如何构造此代码以最好地适应潜在的异步事件的处理？

FreeRTOS不会对应用程序设计者强加任何特定的事件处理策略，但提供了功能，允许选择的策略以简单且可维护的方式实现。

重要的是要区分任务的优先级和中断的优先级之间的区别：

- 任务具有与FreeRTOS运行的硬件无关的软件特性。任务的优先级由应用程序编写者在软件中分配，并由软件算法（调度程序）决定哪个任务处于运行状态。
- 尽管以软件编写，但中断服务例程具备硬件特性，因为硬件控制哪个中断服务例程将运行以及何时运行。只有当没有ISR运行时，任务才会运行，因此最低优先级中断将可以中断最高优先级任务，而任务无法抢占ISR。

所有支持FreeRTOS运行的体系结构都能够处理中断，但与中断进入和中断优先级分配相关的细节因体系结构而异。

#### 范围

本章旨在让读者充分了解以下内容：

- 可以在中断服务例程中使用哪些FreeRTOS API函数。
- 推迟中断处理到一个任务的方法。
- 如何创建和使用二值信号量和计数信号量。
- 二值信号量和计数信号量之间的区别。
- 如何使用队列在中断服务例程中传递数据。
- 一些FreeRTOS端口可用的中断嵌套模型。

### 在中断服务例程中使用FreeRTOS API

#### 中断安全的API

通常需要从中断服务例程（ISR）中使用FreeRTOS API函数提供的功能，但许多FreeRTOS API函数执行在ISR内是无效的操作，其中最显著的操作是将调用API函数的任务置于阻塞状态；如果从ISR中调用API函数，则表示它不是从任务中调用的，因此没有可置于阻塞状态的调用任务。FreeRTOS通过提供某些API函数的两个版本来解决此问题；一个版本供任务使用，另一个版本供ISR使用。用于从ISR使用的函数在其名称后附加了“FromISR”。

注意：永远不要从ISR中调用没有“FromISR”在其名称中的FreeRTOS API函数。

#### 使用单独的中断安全API的好处

拥有一个单独的中断API允许任务代码更加高效，ISR代码更加高效，并且中断入口更加简单。为了理解为什么，考虑替代解决方案，即提供每个API函数的单一版本，可从任务和ISR中调用。如果来自任务和ISR可以从相同版本的API函数中调用，则会出现以下情况：

- API函数需要额外的逻辑来确定它们是从任务还是ISR中调用的。额外的逻辑会引入函数的新路径，使函数变得更长、更复杂且更难测试。
- 某些API函数参数在从任务调用函数时将变得过时，而另一些在从ISR调用函数时将变得过时。
- 每个FreeRTOS端口都需要提供一个确定执行上下文（任务或ISR）的机制。
- 在不容易确定执行上下文（任务或ISR）的体系结构上，需要提供额外的、低效的、更复杂的使用方式，并且不符合标准的中断入口代码，以允许由软件提供执行上下文。

#### 使用单独的中断安全API的劣势

拥有某些API函数的两个版本允许任务和ISR更加高效，但引入了一个新问题；有时需要从任务和ISR中调用不是FreeRTOS API的一部分但使用了FreeRTOS API的函数。 通常，只有在集成第三方代码时才会出现问题，因为那是唯一软件设计不受应用程序编写者控制的时候。如果这确实成为问题，那么可以使用以下技术之一来解决问题：

1. 推迟中断处理到一个任务中[^11]，以便API函数只能从任务上下文中调用。
2. 如果您使用的FreeRTOS端口支持中断嵌套，则使用以“FromISR”结尾的API函数版本，因为该版本可以从任务和ISR中调用（反之不成立，不能从ISR中调用不以“FromISR”结尾的API函数）。
3. 第三方代码通常包括一个RTOS抽象层，可以用来测试从哪个上下文（任务或中断）中调用函数，然后调用适合上下文的API函数。

[^11]:延迟中断处理将在本书的下一节中介绍。

#### xHigherPriorityTaskWoken参数

本节介绍了xHigherPriorityTaskWoken参数的概念。如果您目前还没有完全理解这一部分，不要担心，因为后续章节将提供实际示例。

如果一个中断执行了上下文切换，那么当中断退出时运行的任务可能与中断进入时运行的任务不同——中断会打断一个任务，但返回到不同的任务。

一些FreeRTOS API函数可以将任务从阻塞状态移到就绪状态。这已经在诸如xQueueSendToBack()之类的函数中看到过，如果有一个任务在阻塞状态等待主题队列上的数据变为可用，它将取消阻塞一个任务。

如果被FreeRTOS API函数解除阻塞的任务的优先级高于运行中的任务的优先级，则根据FreeRTOS的调度策略，应该发生任务切换。实际发生任务切换的时间取决于调用API函数的上下文：

- 如果API函数是从任务中调用的

  如果在FreeRTOSConfig.h中设置了configUSE_PREEMPTION为1，那么切换到更高优先级任务会自动在API函数内部发生，即在API函数退出之前。这在图 43中已经看到过，在写入定时器命令队列时，会在写入命令队列的函数退出之前切换到RTOS守护程序任务。

- 如果API函数是从中断中调用的

  在中断中，不会自动发生到更高优先级任务的切换。相反，将设置一个变量，以通知应用程序编写者应执行上下文切换。中断安全的API函数（以“FromISR”结尾的函数）具有用于此目的的名为pxHigherPriorityTaskWoken的指针参数。 如果应该执行上下文切换，那么中断安全的API函数将将*pxHigherPriorityTaskWoken设置为pdTRUE。为了能够检测到这一点，pxHigherPriorityTaskWoken指向的变量必须在第一次使用之前初始化为pdFALSE。 如果应用程序编写者选择不从ISR中请求上下文切换，则更高优先级任务将保持在就绪状态，直到调度程序再次运行为止，最坏情况下会在下一个tick中断期间发生。 FreeRTOS API函数只能将*pxHighPriorityTaskWoken设置为pdTRUE。如果ISR调用了多个FreeRTOS API函数，那么可以在每个API函数调用中将相同的变量作为pxHigherPriorityTaskWoken参数传递，而且只需要在第一次使用之前将变量初始化为pdFALSE。

中断安全版本的API函数内部不会自动发生上下文切换的原因有以下几个：

1. 避免不必要的上下文切换

   一个中断在需要任务执行任何处理之前可能会执行多次。例如，考虑一个任务处理由中断驱动的UART接收的字符串的情况；如果每次接收到一个字符时UART ISR都切换到任务，那将是浪费的，因为任务只有在完整字符串被接收后才需要执行处理。

2. 控制执行顺序

   中断可以不定期地在不可预测的时间发生。FreeRTOS的高级用户可能希望在其应用程序的特定点临时避免对不可预测的任务切换，尽管这也可以通过FreeRTOS调度程序锁定机制实现。

3. 可移植性

   这是可以在所有FreeRTOS端口上使用的最简单机制。

4. 效率

   针对较小处理器体系结构的端口只允许在ISR的最后请求上下文切换，而取消此限制将需要额外且更复杂的代码。它还允许在同一ISR内多次调用FreeRTOS API函数而不会生成多个上下文切换请求。

5. 在RTOS tick中断中执行

   正如本书后面将要看到的，可以将应用程序代码添加到RTOS tick中断中。尝试在tick中断中进行上下文切换的结果取决于正在使用的FreeRTOS端口。在最好的情况下，它将导致不必要地调用调度程序。

pxHigherPriorityTaskWoken参数的使用是可选的。如果不需要它，请将pxHigherPriorityTaskWoken设置为NULL。

#### portYIELD_FROM_ISR()和portEND_SWITCHING_ISR()宏

本节介绍了用于从ISR请求上下文切换的宏。如果您目前还没有完全理解这一部分，不要担心，因为后续章节将提供实际示例。

taskYIELD()是一个可在任务中调用的宏，用于请求上下文切换。portYIELD_FROM_ISR()和portEND_SWITCHING_ISR()都是taskYIELD()的中断安全版本。portYIELD_FROM_ISR()和portEND_SWITCHING_ISR()的使用方式相同，执行相同的操作[^12]。一些FreeRTOS端口只提供其中一个宏。较新的FreeRTOS端口提供了两个宏。本书中的示例使用portYIELD_FROM_ISR()。

---

```c
portEND_SWITCHING_ISR( xHigherPriorityTaskWoken );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单87. portEND_SWITCHING_ISR()宏。</div> </center>

---

```c
portYIELD_FROM_ISR( xHigherPriorityTaskWoken );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单88. portYIELD_FROM_ISR()宏。</div> </center>

从中断安全的API函数传出的xHigherPriorityTaskWoken参数可以直接用作调用portYIELD_FROM_ISR()的参数。

如果portYIELD_FROM_ISR()的xHigherPriorityTaskWoken参数为pdFALSE（零），则不会请求上下文切换，宏不会产生任何效果。如果portYIELD_FROM_ISR()的xHigherPriorityTaskWoken参数不是pdFALSE，则会请求上下文切换，并且处于运行状态的任务可能会更改。即使在中断执行时任务的运行状态发生了更改，中断仍然会返回到处于运行状态的任务。

大多数FreeRTOS端口允许在ISR的任何位置调用portYIELD_FROM_ISR()。少数FreeRTOS端口（主要是较小体系结构的端口）只允许在ISR的最后调用portYIELD_FROM_ISR()。

[^12]:从历史上看，portEND_SWITCHING_ISR()是在需要中断处理程序使用汇编代码包装的FreeRTOS端口中使用的名称，而portYIELD_FROM_ISR()是在允许整个中断处理程序使用C语言编写的FreeRTOS端口中使用的名称。

### 延迟中断处理

通常，将中断服务程序（ISR）保持尽可能短是最佳实践。这样做的原因包括：

- 即使任务被分配了非常高的优先级，它们只有在硬件没有正在服务中断时才会运行。
- ISR可能会干扰任务的启动时间和执行时间，增加"抖动"。
- 根据FreeRTOS运行的架构，当ISR正在执行时,可能无法接受任何新的中断，或者至少是一些新的中断。
- 应用程序编写者需要考虑任务和ISR同时访问资源，如变量、外设和内存缓冲区的后果，并加以保护。
- 一些FreeRTOS端口允许中断嵌套，但中断嵌套可能会增加复杂性并降低可预测性。中断越短，嵌套的可能性就越小。

中断服务程序必须记录中断的原因，并清除中断。由中断引起的任何其他处理通常可以在任务中执行，从而允许中断服务程序尽快退出。这被称为"延迟中断处理"，因为中断引起的处理从ISR中"延迟"到了一个任务中。

将中断处理延迟到任务中还允许应用程序编写者相对于应用程序中的其他任务对处理进行优先级排序，并使用所有的FreeRTOS API函数。

如果中断处理被延迟到的任务的优先级高于任何其他任务的优先级，那么处理将会立即执行，就像处理是在ISR本身中执行的一样。这种情况如图 48所示，其中Task 1是正常的应用程序任务，Task 2是中断处理被延迟到的任务。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure48.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图48. 在高优先级任务中完成中断处理。</div> </center>

在图 48中，中断处理从时间t2开始，实际上在时间t4结束，但只有在时间t2和t3之间的时间在ISR中度过。如果没有使用延迟中断处理，那么在时间t2和t4之间的整个时间都将在ISR中度过。

关于何时最好在ISR中执行由中断引起的所有处理，以及何时最好将部分处理延迟到任务中，没有绝对的规则。将处理延迟到任务中最有用的情况包括：

- 由中断引起的处理不是微不足道的。例如，如果中断只是存储模数转换的结果，那么几乎可以肯定最好在ISR内执行，但如果必须将转换的结果通过软件滤波器传递，那么最好在任务中执行滤波器。
- 中断处理方便执行一个在ISR内无法执行的动作，比如写入控制台或分配内存。
- 中断处理不是确定性的，也就是说事先不知道处理需要多长时间。

接下来的章节将描述和演示迄今为止介绍的概念，包括可以用来实现延迟中断处理的FreeRTOS特性。

### 用于同步的二值信号量

二值信号量的中断安全版本可用于每次特定中断发生时解除一个任务的阻塞，从而有效地将任务与中断同步。这允许将大多数中断事件处理实现在同步任务内，只需在ISR中保留非常快速和短暂的部分。如前一节所述，二值信号量用于将中断处理"延迟"到任务中[^13]。

如先前图 48中演示的，如果中断处理特别时间关键，那么可以将延迟处理任务的优先级设置为确保任务始终抢占系统中的其他任务。然后，可以在ISR中实现一个调用portYIELD_FROM_ISR()的语句，以确保ISR直接返回到中断处理被延迟的任务。这将确保整个事件处理在时间上连续执行（无中断），就好像它都是在ISR内实现的。图 49重复了图48中显示的情景，但更新了文本以描述如何使用信号量来控制延迟处理任务的执行。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure49.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图49. 使用二值信号量实现延迟中断处理。</div> </center>

[^13]:使用直接发送任务通知比使用二值信号量从中断中解除任务的阻塞效率更高。直接发送任务通知将在第9章“任务通知”中介绍。

延迟处理任务使用阻塞的“take”调用信号量来进入阻塞状态，等待事件发生。当事件发生时，ISR使用相同信号量的“give”操作来解除任务的阻塞，以便进行所需的事件处理。

“获取信号量”和“释放信号量”是根据使用场景而有不同含义的概念。在这种中断同步场景中，可以将二值信号量在概念上视为长度为一的队列。队列最多只能包含一个项目，因此始终处于空或满的状态（因此是二进制的）。通过调用xSemaphoreTake()，延迟处理中断的任务有效地尝试从带有阻塞时间的队列中读取，如果队列为空，则导致任务进入阻塞状态。当事件发生时，ISR使用xSemaphoreGiveFromISR()函数将一个令牌（信号量）放入队列中，使队列变满。这会导致任务退出阻塞状态并删除令牌，再次将队列变为空。当任务完成其处理后，它再次尝试从队列中读取，并发现队列为空时，重新进入阻塞状态等待下一个事件。这个过程在图 50中示范。

图 50显示了中断“给”信号量，尽管它没有首先“取”它，以及任务“取”信号量，但从未还回。这就是为什么该场景被描述为在概念上类似于写入和读取队列。它经常会引起混淆，因为它不遵循其他信号量使用场景的相同规则，其中获取信号量的任务必须始终还回信号量，例如第7章“资源管理”中描述的场景。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure50.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图50. 使用二值信号量将任务与中断同步。</div> </center>

#### `xSemaphoreCreateBinary()` API 函数

各种类型的FreeRTOS信号量的句柄都存储在类型为SemaphoreHandle_t的变量中。在使用信号量之前，必须创建它。要创建二值信号量，请使用xSemaphoreCreateBinary() API函数[^14]。

---

```c
SemaphoreHandle_t xSemaphoreCreateBinary( void );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单89. xSemaphoreCreateBinary() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|     返回值     | 如果返回NULL，则表示无法创建信号量，因为没有足够的堆内存可供FreeRTOS分配信号量数据结构。 |
|                | 返回非NULL值表示已成功创建信号量。返回的值应存储为已创建信号量的句柄。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 33. xSemaphoreCreateBinary() 返回值。</div> </center>

[^14]:一些信号量API函数实际上是宏，而不是函数。为简单起见，本书中统一称它们为函数。

#### `xSemaphoreTake()` API 函数

"获取"信号量意味着只有在信号量可用时才能"获取"或"接收"信号量。除了递归互斥量以外，所有各种类型的FreeRTOS信号量都可以使用xSemaphoreTake()函数"获取"。 xSemaphoreTake()不得从中断服务例程中使用。

---

```c
BaseType_t xSemaphoreTake( SemaphoreHandle_t xSemaphore, TickType_t xTicksToWait );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单90. xSemaphoreTake()() API 函数原型。</div> </center>

| 参数名/ 返回值 |                             描述                             |
| :------------: | :----------------------------------------------------------: |
|   xSemaphore   |                       "获取"的信号量。                       |
|                | 信号量由SemaphoreHandle_t类型的变量引用。在使用之前，必须显式创建信号量。 |
|  xTicksToWait  |  如果信号量尚不可用，任务将在阻塞状态等待信号量的最长时间。  |
|                | 如果xTicksToWait为零并且信号量不可用，xSemaphoreTake()将立即返回。 |
|                | 阻塞时间以时钟周期来指定，因此它表示的绝对时间取决于时钟周期。宏pdMS_TO_TICKS()可用于将以毫秒为单位指定的时间转换为以时钟周期为单位指定的时间。 |
|                | 如果将xTicksToWait设置为portMAX_DELAY，且在FreeRTOSConfig.h中将INCLUDE_vTaskSuspend设置为1，任务将无限期等待（没有超时）。 |
|     返回值     |                     有两种可能的返回值：                     |
|                | 1. pdPASS 只有在成功获取信号量时才会返回pdPASS。 如果指定了阻塞时间（xTicksToWait不为零），则可能在信号量未立即可用时将调用任务置于阻塞状态，但信号量在阻塞时间到期之前变为可用。 |
|                | 2. pdFALSE 信号量不可用。 如果指定了阻塞时间（xTicksToWait不为零），则调用任务将被置于阻塞状态以等待信号量变为可用，但阻塞时间在此之前已到期。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 34. xSemaphoreTake()() 返回值。</div> </center>

#### `xSemaphoreGiveFromISR()` API函数

可以使用xSemaphoreGiveFromISR()函数来“给予”二值信号量和计数信号量[^15]。 xSemaphoreGiveFromISR()是xSemaphoreGive()的中断安全版本，因此具有本章开头描述的pxHigherPriorityTaskWoken参数。

---

```c
BaseType_t xSemaphoreGiveFromISR( SemaphoreHandle_t xSemaphore,
                                  BaseType_t *pxHigherPriorityTaskWoken );
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单91. xSemaphoreGiveFromISR()() API 函数原型。</div> </center>

|      参数名/ 返回值       |                             描述                             |
| :-----------------------: | :----------------------------------------------------------: |
|        xSemaphore         |                      被“给出”的信号量。                      |
|                           | 信号量由类型为SemaphoreHandle_t的变量引用，必须在使用之前明确创建。 |
| pxHigherPriorityTaskWoken | 一个信号量可能有一个或多个任务阻塞在等待该信号量变为可用的状态。调用xSemaphoreGiveFromISR()可以使信号量变为可用，从而导致等待信号量的任务离开阻塞状态。如果调用xSemaphoreGiveFromISR()导致一个任务离开阻塞状态，并且已解除阻塞的任务的优先级高于当前正在执行的任务（即被中断的任务），那么在内部，xSemaphoreGiveFromISR()将设置*pxHigherPriorityTaskWoken为pdTRUE。 |
|                           | 如果xSemaphoreGiveFromISR()将此值设置为pdTRUE，通常应在退出中断之前执行上下文切换。这将确保中断直接返回到优先级最高的就绪态任务。 |
|          返回值           |                     有两种可能的返回值：                     |
|                           | 1. pdPASS 只有在xSemaphoreGiveFromISR()的调用成功时才会返回pdPASS。 |
|                           | 2. pdFAIL 如果信号量已经可用，就不能再次给出，xSemaphoreGiveFromISR()将返回pdFAIL。 |

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">表格 35. xSemaphoreGiveFromISR() 参数和返回值。</div> </center>

[^15]:计数信号量将在本书的后面部分进行描述。

#### 示例 16. 使用二值信号量将任务与中断同步

这个示例使用二值信号量从中断服务例程中解除任务的阻塞，从而有效地将任务与中断同步。

一个简单的周期性任务被用来每500毫秒生成一个软中断。由于在某些目标环境中，挂接到真实中断是复杂的，出于方便起见，这里使用了软中断。清单92展示了周期性任务的实现。请注意，任务在生成中断之前和之后都打印了一个字符串。这允许在执行示例时生成的输出中观察执行序列。

---

```c
/* The number of the software interrupt used in this example. The code shown is from the Windows project, where numbers 0 to 2 are used by the FreeRTOS Windows port itself, so 3 is the first number available to the application. */
#define mainINTERRUPT_NUMBER 3
static void vPeriodicTask( void *pvParameters )
{
    const TickType_t xDelay500ms = pdMS_TO_TICKS( 500UL );
    /* As per most tasks, this task is implemented within an infinite loop. */
    for( ;; )
    {
        /* Block until it is time to generate the software interrupt again. */
        vTaskDelay( xDelay500ms );
        /* Generate the interrupt, printing a message both before and after
        the interrupt has been generated, so the sequence of execution is evident
        from the output.
        The syntax used to generate a software interrupt is dependent on the
        FreeRTOS port being used. The syntax used below can only be used with
        the FreeRTOS Windows port, in which such interrupts are only simulated. */
        vPrintString( "Periodic task - About to generate an interrupt.\r\n" );
        vPortGenerateSimulatedInterrupt( mainINTERRUPT_NUMBER );
        vPrintString( "Periodic task - Interrupt generated.\r\n\r\n\r\n" );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单92. 示例16中定期生成软中断的任务的实现。</div> </center>

清单93展示了被延迟处理中断处理的任务的实现 - 通过使用二值信号量与软件中断同步的任务。同样，在任务的每次迭代中都会打印出一个字符串，因此可以从执行示例时生成的输出中明显看出任务和中断的执行顺序。

需要注意的是，虽然清单93中显示的代码对于示例16，其中中断由软件生成，是足够的，但对于由硬件外设生成的中断的情况，它是不足够的。后面的小节描述了如何改变代码的结构，使其适用于与硬件生成的中断一起使用。

---

```c
static void vHandlerTask( void *pvParameters )
{
    /* As per most tasks, this task is implemented within an infinite loop. */
    for( ;; )
    {
        /* Use the semaphore to wait for the event. The semaphore was created
        before the scheduler was started, so before this task ran for the first
        time. The task blocks indefinitely, meaning this function call will only
        return once the semaphore has been successfully obtained - so there is
        no need to check the value returned by xSemaphoreTake(). */
        xSemaphoreTake( xBinarySemaphore, portMAX_DELAY );
        /* To get here the event must have occurred. Process the event (in this
        Case, just print out a message). */
        vPrintString( "Handler task - Processing event.\r\n" );
    }
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单93. 示例16中延迟处理中断的任务的实现（与中断同步的任务）。</div> </center>

如清单94所示的是ISR的实现。这个ISR实际上除了“给予”信号量以解除延迟处理中断的任务的阻塞状态之外，几乎什么都不做。

请注意xHigherPriorityTaskWoken变量的使用。在调用xSemaphoreGiveFromISR()之前，它被设置为pdFALSE，然后在调用portYIELD_FROM_ISR()时用作参数。如果xHigherPriorityTaskWoken等于pdTRUE，portYIELD_FROM_ISR()宏内部将请求上下文切换。

ISR的原型和用于强制上下文切换的宏对于FreeRTOS Windows端口是正确的，对于其他FreeRTOS端口可能会有所不同。请参考FreeRTOS.org网站上的特定端口文档页面以及FreeRTOS下载提供的示例，查找您使用的端口所需的语法。

与FreeRTOS运行的大多数架构不同，FreeRTOS Windows端口要求ISR返回一个值。提供Windows端口的portYIELD_FROM_ISR()宏的实现包括return语句，因此Listing 94没有明确显示返回值。

---

```c
static uint32_t ulExampleInterruptHandler( void )
{
    BaseType_t xHigherPriorityTaskWoken;
    /* The xHigherPriorityTaskWoken parameter must be initialized to pdFALSE as
    it will get set to pdTRUE inside the interrupt safe API function if a
    context switch is required. */
    xHigherPriorityTaskWoken = pdFALSE;
    /* 'Give' the semaphore to unblock the task, passing in the address of
    xHigherPriorityTaskWoken as the interrupt safe API function's
    pxHigherPriorityTaskWoken parameter. */
    xSemaphoreGiveFromISR( xBinarySemaphore, &xHigherPriorityTaskWoken );
    /* Pass the xHigherPriorityTaskWoken value into portYIELD_FROM_ISR(). If
    xHigherPriorityTaskWoken was set to pdTRUE inside xSemaphoreGiveFromISR()
    then calling portYIELD_FROM_ISR() will request a context switch. If
    xHigherPriorityTaskWoken is still pdFALSE then calling
    portYIELD_FROM_ISR() will have no effect. Unlike most FreeRTOS ports, the
    Windows port requires the ISR to return a value - the return statement
    is inside the Windows version of portYIELD_FROM_ISR(). */
    portYIELD_FROM_ISR( xHigherPriorityTaskWoken );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单94. 示例16中使用的软件中断的ISR。</div> </center>

主函数(main())创建了二值信号量，创建了任务，安装了中断处理程序，并启动了调度器。其实现如清单95所示。

用于执行中断处理程序的函数的语法是特定于FreeRTOS Windows端口的，对于其他FreeRTOS端口可能会有所不同。请参考FreeRTOS.org网站上的特定端口文档页面以及FreeRTOS下载提供的示例，查找您使用的端口所需的语法。

---

```c
int main( void )
{
    /* Before a semaphore is used it must be explicitly created. In this example
    a binary semaphore is created. */
    xBinarySemaphore = xSemaphoreCreateBinary();
    /* Check the semaphore was created successfully. */
    if( xBinarySemaphore != NULL )
    {
        /* Create the 'handler' task, which is the task to which interrupt
        processing is deferred. This is the task that will be synchronized with
        the interrupt. The handler task is created with a high priority to ensure
        it runs immediately after the interrupt exits. In this case a priority of
        3 is chosen. */
        xTaskCreate( vHandlerTask, "Handler", 1000, NULL, 3, NULL );
        /* Create the task that will periodically generate a software interrupt.
        This is created with a priority below the handler task to ensure it will
        get preempted each time the handler task exits the Blocked state. */
        xTaskCreate( vPeriodicTask, "Periodic", 1000, NULL, 1, NULL );
        /* Install the handler for the software interrupt. The syntax necessary
        to do this is dependent on the FreeRTOS port being used. The syntax
        shown here can only be used with the FreeRTOS windows port, where such
        interrupts are only simulated. */
        vPortSetInterruptHandler( mainINTERRUPT_NUMBER, ulExampleInterruptHandler );
        /* Start the scheduler so the created tasks start executing. */
        vTaskStartScheduler();
    }
    /* As normal, the following line should never be reached. */
    for( ;; );
}
```

---

<center>   <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">清单95. 示例16中main()函数的实现。</div> </center>

示例16产生了如图 51所示的输出。如预期，vHandlerTask()在中断生成后立即进入Running状态，因此任务的输出分割了周期性任务产生的输出。图 52提供了更多解释。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure51.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图51. 执行示例16产生的输出。</div> </center>

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure52.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图52. 示例16的执行顺序。</div> </center>

#### 改进示例16中使用的任务实现

示例16使用了一个二值信号量来同步任务和中断。执行顺序如下：

1. 发生中断。

2. 中断服务程序（ISR）执行并“释放”信号量以解除任务的阻塞。

3. 任务在ISR之后立即执行，并“获取”信号量。

4. 任务处理事件，然后尝试再次“获取”信号量——因为信号量尚不可用（另一个中断尚未发生），所以任务进入了阻塞状态。

示例16中使用的任务结构仅在中断以相对较低的频率发生时才足够。要理解为什么，考虑在任务完成对第一个中断的处理之前，如果发生了第二个和第三个中断会发生什么情况：

- 当第二个ISR执行时，信号量将为空，因此ISR将释放信号量，并且任务将在完成对第一个事件的处理后立即处理第二个事件。这种情况如图53所示。
- 当第三个ISR执行时，信号量已经可用，防止ISR再次释放信号量，因此任务将不会知道第三个事件已发生。这种情况如图54所示。

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure53.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图53. 当一个中断发生在任务完成处理第一个事件之前时的情景。</div> </center>

<center>    <img style="border-radius: 0.3125em;    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"     src="./README.assets/figure54.png">    <br>    <div style="color:orange; border-bottom: 1px solid #d9d9d9;    display: inline-block;    color: #999;    padding: 2px;">图54. 两个中断发生在任务完成处理第一个事件之前时的情景。</div> </center>

在示例16中使用的延迟中断处理任务以及在清单93中显示的任务结构，它只在每次调用xSemaphoreTake()之间处理一个事件。这对于示例16是足够的，因为生成事件的中断是由软件触发的，而且发生在可预测的时间。在实际应用中，中断是由硬件生成的，而且发生在不可预测的时间。因此，为了最小化中断被遗漏的机会，延迟中断处理任务必须结构化，以便在每次调用xSemaphoreTake()之间处理所有已经可用的事件[^16]。清单96演示了一个延迟中断处理程序的结构，用于UART的处理，假定UART在每次接收字符时生成接收中断，并且UART将接收到的字符放入硬件FIFO（硬件缓冲区）。

在示例16中使用的延迟中断处理任务还有一个缺点；它在调用xSemaphoreTake()时没有使用超时。相反，任务将portMAX_DELAY作为xSemaphoreTake() xTicksToWait参数传递，这将导致任务无限期地等待（没有超时）直到信号量可用。在示例代码中通常使用无限期超时，因为它们的使用简化了示例的结构，因此使示例更容易理解。然而，在实际应用中，无限期超时通常是不良实践，因为它们使从错误中恢复变得困难。例如，考虑以下情况：一个任务正在等待中断给出一个信号量，但硬件中的错误状态阻止了中断的生成： 

* 如果任务等待而没有超时，它将不会知道错误状态，并且将永远等待下去。

* 如果任务使用了超时等待，那么当超时到期时，xSemaphoreTake()将返回pdFAIL，任务随后可以在下次执行时检测并清除错误。这种情况也在清单96中演示。

[^16]:另外，可以使用计数信号量或直接任务通知来计数事件。计数信号量将在下一节中进行描述。直接任务通知将在第9章“任务通知”中进行描述。直接任务通知是首选的方法，因为它们在运行时和RAM使用方面都最为高效。

