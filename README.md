# GoogleCppStyleGuideCN
Google C++ Style Guide CN 谷歌C++代码风格指南中文翻译
Translator: [shendeguize@github](
https://github.com/shendeguize)

Link: [
https://github.com/shendeguize/GoogleCppStyleGuideCN](
https://github.com/shendeguize/GoogleCppStyleGuideCN)


本翻译囿于水平，可能有不准确的地方，欢迎指出，谢谢大家  
(p.s. 谷歌翻译感觉有点厉害.)  

*如有引用,请注明出处*
## 目录
- [GoogleCppStyleGuideCN](#googlecppstyleguidecn)
  - [目录](#目录)
  - [内容列表](#内容列表)
  - [0. 背景](#0-背景)
    - [0.1. 代码风格指南的目标](#01-代码风格指南的目标)
  - [1. C++版本](#1-c版本)
  - [2. 头文件](#2-头文件)
    - [2.1. 独立的头文件](#21-独立的头文件)
    - [2.2. `#define`保护](#22-define保护)
    - [2.3. 包含你使用的内容](#23-包含你使用的内容)
    - [2.4. 前向声明](#24-前向声明)
    - [2.5. 内联函数](#25-内联函数)

## 内容列表
***TODO***

## 0. 背景
C++是谷歌开源项目的主要开发语言之一.想每个C++程序员所知的那样.这门语言有很多强力的特性,但伴随着它的强力而来的是它的复杂性,而这种复杂性使得代码更容易产生bug并更难读懂和维护.

本篇指南的目标是通过具体地描述在C++开发中应做和不应做的事情来管理C++的复杂性.这些规则的存在是为了保证代码可管理的同时也允许程序要能够有生产力地使用C++语言特性.

**代码风格**或称为可读性,是我们用于管理C++代码的约定.术语"样式"稍有一些用词不当,因为这些预定不仅涵盖了源文件的格式,也有更多的内容.

大多数谷歌开发的的开源项目遵循本规定.

注意:本指南不是C++教程,我们默认读者是熟悉C++语言的.

### 0.1. 代码风格指南的目标

为什么会有这份文件?

我们认为这篇指南应该达到一些核心目标.这些目标是构成所有单独规则的根本**原因**.首先我们提出这些想法,并希望切实地讨论并让我们广泛的 社区能够更清楚的了解为什么制定这些规则以及为什么有一些特殊情况.如果你能够理解每一项规则所服务的目标是什么,那么对于每个人来说,(部分规则)何时可以豁免,以及哪些规定或可选的规则是需要用于修改本指南中的某一规定的.

我们目前看到的本篇代码风格指南的目标如下:

+ 风格规定应该有其作用  
    风格规定的收益必须足够大,大到足以要求我们所有的工程师记住它们.而这些收益是与我们不遵循这些规定而生产的代码库进行对比得知的.因此某一条针对非常有害的行为所制定的规定仍然可能有比较小的收益,如果人们不太可能这样做的话.这一原则更多解释了那些我们没有解释的规定,而非我们所提出的规定.例如`goto`违反了很多下属的原则,但是因为其实已经几乎消失了,所以本篇指南不对其进行讨论.
+ 为了读者优化,而非为了编写者优化
    我们的代码库(以及大多数提交到代码库的独立组件)预计会维持相当长的时间.因此,现对于编写代码而言,花在阅读代码上的时间其实会更多.显然,我们选择去优化以适配普罗软件工程师(average software engineer)的阅读,维护和调试我们代码库总代码的体验,而非为了适配编写上述代码时的轻松体验."为读者留下痕迹"(Leave a trace for the reader)就是这一原则的常见子要点:当代码中有一些出乎意料或者不常见的事情要发生的时候(例如指针所有权的转移时),要给使用这一点的读者留下文本提示是很重要的.(`std::unique_ptr`明确地展示了在调用时的所有权转移).
+ 与现有代码风格一致
    与我们代码库中的代码使用统一的风格能够让我们更多专注在其他(更重要)的问题上.一致性也实现了自动化:那颗可以格式化你的代码或者调整你的`#include`的工具只能在你的代码和工具要求的一致的情形下才能正确工作.在许多情况下,"保持一致"这一点可以归结为"选一个就不再担心它".容许灵活性的可能收益是不如人们在这一点上争执所带来的消耗大的.不过一致性也是存在一些局限性的,如果没有明确的技术论据或者或者长期的方向时,这可能是个很好的决胜局(tie breaker).更多时候是在本地应用(每个文教或一系列紧密相关的接口中).一致性不应成为衡量来使用旧有方式而不考虑使用新风格的好处或者衡量随着事件使用更新的代码风格覆盖代码库的趋势时的考量点.
+ 适当的情况下,与更广阔的C++社区保持一致.
    与其他组织使用的C++方式保持一致是有价值的,其原因是和与我们代码库保持一致性的原因一致.如果C++标准的某一特性解决了一个问题或者某一常用语广为人知并被接受,那么就有使用它的理由.或者有时标准特性或常用语是有些瑕疵的,或者设计时没有考虑到我们代码哭的需求.在这些情形下(如下所述)限制或者禁止标准特性是合理的.在有些情况下,我们更喜欢使用本地或第三方库而非C++标准库,这要么出于已知的优势或出于迁移代码库到标准接口时的优势不足.
+ 避免出乎意料或者危险的构造
    C++有一些特性是初看到时很出乎意料或者难以想见的危险的.一些代码规范限制是为了避免陷入这些陷阱而设定的.放弃这些限制的门槛是很高的,因为放弃这些规则常常直接危害到了程序的正确性.
+ 避免使用普通C++程序员认为是有技巧或者难以维护的结构
    C++有一些特性因其所引入复杂性通常是不建议使用的.在广受使用的代码里,使用更有技巧的语言结构是可以接受的,因为因为任何更复杂的实现会犹豫期广发的使用而倍增,而理解这一复杂性的成本并不会在处理代码库的新的部分时需要再次付出.如有疑问,那么可以通过询问项目负责人来寻求不受此规则限制的权利.这对于我们的代码库来说尤为重要,因为代码负责人和团队成员经常变化:即使使用某段代码的每个人限制都能理解这段代码,也很难保证若干年后还能理解.
+ 注意我们的规模
    我们的代码库包含上亿行代码,我们用用数千位工程师,对一位工程师而言,一些错误或者简化代价非常高昂.例如,避免全局命名空间是非常重要的:上亿行的代码库中的命名冲突是很难处理的,而且如果每个人都向全局命名空间里丢东西,这样的冲突也无法避免.
+ 必要时考虑优化
    有些时候性能优化是必要且合理的,即使这会与本文所述的其他原则相冲突.

这篇文档的目的是提供个在合理限制下最大程度的指导.一如既往地,尝试和良好的品味优先.再次我们参考了整个谷歌C++社区的既有惯例而非您个人或您团队的喜好.请对使用机巧或不常见的构造保持缓一或者拒绝:如无禁止即为许可.善用判断力,如果您不确定,请不要犹豫,向您的项目负责人寻求额外意见.

## 1. C++版本
目前,代码应该基于C++17,即不应使用C++2x的特性.本篇指南所基于的C++版本会(积极地)随着事件演进.

不要使用[非标准拓展](https://google.github.io/styleguide/cppguide.html#Nonstandard_Extensions).

在使用C++14和C++17特性之前,考虑对于其他环境的可迁移性.

## 2. 头文件
通常来说,每个`.cc`文件都应关联到一个`.h`头文件.不过也有一些常见的例外情况,例如单元测试或者一些只包含一个`main()`的小的`.cc`文件.

正确的使用头文件能够让你代码的可读性,代码量和性能都有很大的不同.

### 2.1. 独立的头文件
头文件应该是独立的(单独编译)并在命名为`.h`文件.用于包含的非头文件的文件应命名为`.inc`文件,并应慎用.

所有的头文件应该是独立的.用户和重构工具不必遵循特定条件以包含这些头文件.集体而言,一个头文件应该有[头文件保护](https://google.github.io/styleguide/cppguide.html#The__define_Guard)并且包含所有其他所需的头文件.

最好将模板和内联函数的定义和声明写在同一文件中.这些结构的定义必须被每个调用他们的`.cc`文件所包含,否则程序可能在某些构建配置中链接失败.如果声明和定义分别为永不同的文件里,对声明的包含应该传递性的包含定义.不要将这些定义移动到分别的头文件中(例如`-inl.h`).过去这很常见,但是现在不再允许了.

有些罕见的情形,一个文件是为了用以被包含的,切不是独立的.通常这些文件在不寻常的位置被包含,例如其他文件的中部.这一情形下,可能不需要遵循[头文件保护](https://google.github.io/styleguide/cppguide.html#The__define_Guard)并且包含所有其他所需的头文件,可能不需要包含它的前置需求.请将这些文件命名为`.inc`.请谨慎使用,如果可能的话优先使用独立的头文件.

### 2.2. `#define`保护
所有的头文件应有`#define`保护以避免多次包含.命名格式应为`<PROJECT>_<PATH>_<FILE>_H_`.

为了办证唯一性,这一保护相应基于项目结构树的完整路径.例如在`foo`项目中的`foo/src/bar/baz.h`文件应该有下述保护行:

```C++
#ifndef FOO_BAR_BAZ_H_
#define FOO_BAR_BAZ_H_

...

#endif  // FOO_BAR_BAZ_H_
```

### 2.3. 包含你使用的内容
如果一个源文件或者头文件引用了其他地方定义的符号,那么这一文件就应直接包含提供该符号声明或定义的头文件.除此之外不应以任何理由包含头文件.

不要相信传递包含.传递包含使得人们会移除他们头文件里不必要的`#include`语句而不会破坏客户端.这一作用域相关联的头文件.`foo.cc`应包含`bar.h`,如果`foo.cc`使用了`bar.h`中的符号即使`foo.h`已经包含了`bar.h`

### 2.4. 前向声明
如果可能的话,避免使用前向声明.[而是包含你所需要的头文件](https://google.github.io/styleguide/cppguide.html#Include_What_You_Use).

**定义:**

前向声明是声明一个实体而不关联其定义.

```C++
// In a C++ source file:
class B;
void FuncInB();
extern int variable_in_b;
ABSL_DECLARE_FLAG(flag_in_b);
```

**优点:**

+ 前向声明可以节省编译时间,因为`#include`强制编译器打开更多的文件并处理更多的输入.
+ 前向声明可能节省不必要的重复编译.`#include`会因为不相关的头文件改动更多的强制你的代码重复编译.

**缺点:**

+ 前向声明可能会隐藏依赖关系,使得用户代码在头文件有变更时买跳过必要的重编译.
+ 使用前向编译而非`#include`可能使得自动化工具很难发现符号定义的模块.
+ 对库的后需更改可能会使得前向声明损坏.函数和模板的前向声明会让头文件所有者无法对他们的API进行其他兼容性修改,例如拓展其参数类型,增加有默认值的模板参数或者迁移到新的命名空间.
+ 对`std::`命名空间的前向声明符号可能会产生未定义的行为.
+ 很难确定是否需要一个前向声明还是一个完整的`#include`.将`#include`替换为前向声明可能会悄无声息的改变代码的含义:
    
    ```C++
    // b.h:
    struct B {};
    struct D : B {};

    // good_user.cc:
    #include "b.h"
    void f(B*);
    void f(void*);
    void test(D* x) { f(x); }  // calls f(B*)
    ```

    如果`#include`被替换为对`B`和`D`以及`test()`的前向声明,则会调用`f(void*)`.
+ 前向声明一个头文件中的多个符号可能比单纯的#include`还要复杂.
+ 调整代码以支持前向声明`(例如指针成员而非对象成员)可能会使得代码缓慢且复杂.

**建议:(Decision,后续都译为建议)**

尽量避免前向声明在其他项目中定义的实体.

### 2.5. 内联函数

只在内联函数很小,也就是10行以内时才定义内联函数.

**定义:**

你可以以这样的方式声明函数,即编译器内联展开他们而不是像正常的调用机制一样调用它们.

**优点:**

内联函数可以创建更有效的对象代码,只要内联函数很小.对于内联访问器或变更器或者其他短小而性能优先的函数可以随意使用.

**缺点:**

过度使用内联函数实际上会让程序更慢.取决用程序大小,内联可能会使得代码量有增有减.在现代处理器上,更小的代码通常会由于对指令缓存更好的利用而运行得更快.

**建议:**

合理的经验法则是如果内联函数超过10行就不要使用.注意析构函数常常要比它们看起来的要长.这是由于有隐式的成员或者基类析构函数被调用.

另一个有用的经验法则是通常有循环或判断语句的内联函数不够高效(除非在多数情况下,这一循环或判断语句从不被执行).

很重要的事情是了解函数并不是总是内敛的,即使声明如此.例如虚函数或者递归函数通常不内联.通常递归函数不应该内联.内联一个虚函数的主要原因是为了将其定义放在类内,或是为了方便或者为了描述其行为,例如访问器和修改器.



==翻译工作区:==
**定义:**



**优点:**



**缺点:**



**建议:**

