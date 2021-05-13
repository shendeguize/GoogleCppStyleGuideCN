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
    - [2.6. 包含的名称和顺序](#26-包含的名称和顺序)
  - [3. 作用域](#3-作用域)
    - [3.1. 命名空间](#31-命名空间)
    - [3.2. 内部链接](#32-内部链接)
    - [3.3. 非成员,静态成员和全局函数](#33-非成员静态成员和全局函数)
    - [3.4. 局部变量](#34-局部变量)
    - [3.5. 静态和全局变量](#35-静态和全局变量)
      - [关于析构的建议](#关于析构的建议)
      - [关于初始化的建议](#关于初始化的建议)
      - [普遍模式](#普遍模式)
    - [3.6. 线程局部变量](#36-线程局部变量)
  - [4. 类](#4-类)
    - [4.1. 在构造器中完成工作](#41-在构造器中完成工作)

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

### 2.6. 包含的名称和顺序

请按照下述顺序包含头文件:
+ 相关头文件
+ C系统头文件
+ C++标准库头文件
+ 其他库文件
+ 你的项目库头文件

所有的项目头文件应该项目根目录的子目录而非使用目录别名`.`(当前目录)或者`..`(上级目录).例如`google-awesome-project/src/base/logging.h`应当以下述方式被包含:

```C++
#include "base/logging.h"
```

在`dir/foo.cc`或`dir/foo_test.cc`(用于实现或者测试`dir2/foo2.h`中内容)中,包含目录应如下排序:

+ `dir2/foo2.h`
+ 一行空行
+ C系统头文件(更具体而言:带`<>`的`.h`拓展) 例如`<unistd.h>`,`<stdlib.h>`.
+ 一行空行
+ C++标准库头文件(无文件拓展名)例如`<algorithm>`,`<cstddef>`.
+ 一行空行
+ 其他库的`.h`头文件
+ 你项目的`.h`头文件

使用空行划分每个非空的组.

按照这一顺心,如果相关的头文件`dir2/foo2.h`忽略了任意必须的包含内容,对`dir/foo.cc`或者`dir/foo_test.cc`的构建就会失败.因此这一规则确保了是使用这些文件的人首先看到构建失败而不是其他无辜的使用者遇到问题.

`dir/foo.cc`和`dir2/foo2.h`通常在同一目录下,例如`base/basictypes_test.cc`和`base/basictypes.h`但是有时也可能在不同的目录下.

主要C头文件例如`stddef.h`本质上与其C++标准头文件(`cstddef`)可以互换.两种样式都可以接受,但请与既有代码保持一致性.

在包含列表的每一部分,都应该依照字母顺序排列.注意,老式的代码可能不符合这一规范,在方便时应做修改.

例如,`google-awesome-project/src/foo/internal/fooserver.cc`的包含列表应如下:

```C++
#include "foo/server/fooserver.h"

#include <sys/types.h>
#include <unistd.h>

#include <string>
#include <vector>

#include "base/basictypes.h"
#include "base/commandlineflags.h"
#include "foo/server/bar.h"
```

**例外情况:**

有时,指定系统的代码需要条件化包含.这样的代码可能会在其他包含项后添加条件包含.当然请确保你指定系统的代码小而且本地化,例如:

```C++
#include "foo/public/fooserver.h"

#include "base/port.h"  // For LANG_CXX11.

#ifdef LANG_CXX11
#include <initializer_list>
#endif  // LANG_CXX11
```

## 3. 作用域

### 3.1. 命名空间

**定义:**

除少数例外情况以外,代码应在命名空间内.命名空间依据项目名以及可能的路径具有独有的名称.不要使用`using`指令(例如`using namespace foo`).不要使用内联命名空间.有关匿名命名空间,参见[内部链接](https://google.github.io/styleguide/cppguide.html#Internal_Linkage).

**优点:**

命名空间将全局作用域细分为不同的命名空间,这对于避免全局作用域下的命名冲突很有帮助.

命名空间同理一种在大型程序中避免命名冲突的方法,同事允许大多数可以合理使用简短的名称.

例如,如果两个不同的项目在全局作用域内都有类`Foo`,这些符号可能在编译或者运行时产生冲突.如果每个项目都把他们的代码放置于命名空间里,那么`project1::Foo`和`project2::Foo`现在就是不冲突不同符号.在每个项目命名空间内的代码也能够据需引用`Foo`而不用使用前缀.

内联命名空间会自动的将他们的名字置于封闭作用域内.请思考下述样例代码段:

```C++
namespace outer {
inline namespace inner {
  void foo();
}  // namespace inner
}  // namespace outer
```

表达式`outer::inner::foo()`和`outer::foo()`是可替换的.内联命名空间主要用于跨版本ABI兼容性.

**缺点::**

命名空间可能会造成混淆,因为命名空间会让搞清楚名称所指定义的机制变得复杂.

内联命名空间尤其会造成混淆因为名称实际上并不受限于其所声明的命名空间.内联命名空间只在一些大型的版本控制策略中有用.

在某系情况下,有必要使用完全限定的名称来重复引用符号.对于深层次嵌套的命名空间,这可能会引发很多混乱.

**建议:**

命名空间应按下述方式使用:

+ 遵循[命名空间命名](https://google.github.io/styleguide/cppguide.html#Namespace_Names)中的规定
+ 应按照下述样例所示,用注释结束多行命名空间.
+ 命名空间应在`include`和`gflags`定义或生命和其他命名空间中类的前向声明之后包含完整代码.

    ```C++
    // In the .h file
    namespace mynamespace {

    // All declarations are within the namespace scope.
    // Notice the lack of indentation.
    class MyClass {
    public:
    ...
    void Foo();
    };

    }  // namespace mynamespace
    ```

    ```C++
    // In the .cc file
    namespace mynamespace {

    // Definition of functions is within scope of the namespace.
    void MyClass::Foo() {
    ...
    }

    }  // namespace mynamespace
    ```
    更复杂的`.cc`文件可能有额外的细节,例如`flags`或者`using`声明:

    ```C++
    #include "a.h"

    ABSL_FLAG(bool, someflag, false, "dummy flag");

    namespace mynamespace {

    using ::foo::Bar;

    ...code for mynamespace...    // Code goes against the left margin.

    }  // namespace mynamespace
    ```
+ 要将生成协议消息信息(protocol message)的代码放在命名空间内,在`.proto`文件中使用`package`表示符指明.参见[协议缓冲区包](https://developers.google.com/protocol-buffers/docs/reference/cpp-generated#package).
+ 不要在命名空间`std`内声明任何内容,包括不要前向声明标准库的类.声明命名空间`std`内的实体是一个未定义的行为,即不可以值得.为了声明标准库中的实体,请包含适合的头文件.
+ 不应该使用(may not)`using`指令来让命名空间内的所有名称可用.:
    ```C++
    // Forbidden -- This pollutes the namespace.
    using namespace foo;
    ```
+ 除非在显示表明的只在内部使用的命名空间中以外,都不要(do not)使用命名空间别名.因为导入到头文件中的命名空间里的任何内容都会是改文件导出公共API的一部分.
    ```C++
    // Shorten access to some commonly used names in .cc files.
    namespace baz = ::foo::bar::baz;
    ```
    ```C++
    // Shorten access to some commonly used names (in a .h file).
    namespace librarian {
    namespace impl {  // Internal, not part of the API.
    namespace sidetable = ::pipeline_diagnostics::sidetable;
    }  // namespace impl

    inline void my_inline_function() {
    // namespace alias local to a function (or method).
    namespace baz = ::foo::bar::baz;
    ...
    }
    }  // namespace librarian
    ```
+ 不要使用内联命名空间

### 3.2. 内部链接

当在一个`.cc`文件内的定义不需要在文件外进行引用时,可以通过把它们放到匿名命名空间内或者将他们声明为`static`来进行内部链接.不要在`.h`文件中使用任何一种这样的构造方式.

**定义:**

通过置于匿名命名空间,所有的声明都可以被设为内部链接.函数和变量也可以通过声明为`static`以设为内部链接.这意味着任何你声明的内容不能被其他文件访问.如果一个其他文件声明了一些同名的内容,那么两个实体是完全独立的.

**建议:**

对于那些不会在任何其他地方被引用的代码,鼓励在`.cc`文件内使用内部链接.不要再`.h`文件里使用内部链接.

按照显式命名空间一样的格式来使用命名空间.在结尾注释里,让命名空间留空即可:

```C++
namespace {
...
}  // namespace
```

### 3.3. 非成员,静态成员和全局函数

优先将非成员函数放入命名空间里,尽量少使用完全全局的函数.不要单纯使用一个类来管理静态成员.类的静态方法应该通常与该类的实例或这个类的静态数据关联.

**优点:**

非成员和静态成员函数在一些情况下很有用.将非成员函数放在命名空间内能够避免污染全局命名空间.

**缺点:**

非成员和静态成员函数可能(may)在作为一个新类的成员时是有用的,尤其它们访问外部资源或者有重要的依赖项时.

**建议:**

有的时候定义一个不绑定到类实例的函数很有用.例如一个函数可能既是静态成员也是非成员函数.非成员函数应该不依赖于外部变量,应该几乎总存在于命名空间内.不要只是为了组织静态成员而创建类,这和仅仅给这些命名一个通用的前缀没有什么不同,而且这种分组通常不必要.

如果你定义一个非成员函数而且只在它的`.cc`文件内需要,那么使用[内部链接](https://google.github.io/styleguide/cppguide.html#Internal_Linkage)来限制其作用域.

### 3.4. 局部变量

将函数的变量限制在尽可能小的作用域中,并在声明中初始化变量.

C++允许你在函数的任何地方声明变量.我们建议您尽可能在局部范围内声明这些变量,尽可能靠近首次使用它的位置.这会让使用者更容易找到声明并发现这个变量的类型是什么以及被初始化成了什么.尤其是应该使用初始化而不是声明再赋值,例如:

```C++
int i;
i = f();      // Bad -- initialization separate from declaration.
```

```C++
int j = g();  // Good -- declaration has initialization.
```

```C++
std::vector<int> v;
v.push_back(1);  // Prefer initializing using brace initialization.
v.push_back(2);
```

```C++
std::vector<int> v = {1, 2};  // Good -- v starts initialized.
```

`if`,`while`和`for`语句等所需的变量通常在这些语句中声明,所有这些变量被限制在这些作用域内,例如:

```C++
while (const char* p = strchr(str, '/')) str = p + 1;
```

有一个需要注意的事情:如果一个变量是一个对象,它的构造器每次它进入作用域和被创建时都被调用,而析构器则在其每次离开作用于都被调用.

```C++
// Inefficient implementation:
for (int i = 0; i < 1000000; ++i) {
  Foo f;  // My ctor and dtor get called 1000000 times each.
  f.DoSomething(i);
}
```

```C++
Foo f;  // My ctor and dtor get called once each.
for (int i = 0; i < 1000000; ++i) {
  f.DoSomething(i);
}
```

### 3.5. 静态和全局变量

具有[静态存储持续时间](http://en.cppreference.com/w/cpp/language/storage_duration#Storage_duration)的对象时禁止使用的,除非他们是[可平凡析构的(trivially destructible)](http://en.cppreference.com/w/cpp/types/is_destructible).非正式的来说,这意味着析构器什么都不做,即使考虑到成员和积累析构器也是如此.正式的来讲,这一位置该类型没有用户定义析构函数或者虚析构函数而且所有的基类和费静态成员都可平凡析构.静态函数局部变量可以进行动态生成.不建议对静态类成员变量或者命名空间内变量使用动态初始化,但是在一些特定情况下也是允许使用的,具体细节可以参考下述内容.

一个经验法则:如果单独考虑声明,可以是`constexpr`,那么全局变量就符合这些要求.

**定义:**

每一个对象都有其*存储周期*,并相关于其生命周期.有静态存储周期的对象从其初始化到程序结束期间生存.这些对象在命名空间作用域内表现为变量("全局变量),例如类的静态数据成员,或作为带有`static`标识声明的函数内部变量.函数局部静态变量是在控制流首次通过其声明时初始化的.所有的其他带有静态存储周期的对象时作为成语启动的一部分初始化的.所有带有静态存储周期的对象都在程序退出时校徽(这发生在未合并的线程终止之前).

初始化可以(may)是*动态的*,这意味着在初始化中会有一些不重要的事情发生.(例如,考虑一个构造器声明了内存或者一个变量伴随着其进程id初始化).另一种类型初始化是*静态*初始化.这两者并不那么相对立.静态初始化*总是(always)*发生在有静态存储周期的对象上(要么初始化这个对象到某一常数或者到代表性的0字节组).而动态初始化在这之后发生,如果需要的话.

**优点:**

全局和静态变量对于大量的使用而言是很有效的.命名常量,某些转换单元的内部辅助数据结构,命令行标志,注册机制,后台结构等.

**缺点:**

使用动态初始化或者有非平凡析构的全局或静态变量会产生易于导致难以发现的bug的复杂性.动态初始化没有在翻译单元或销毁过程里排序(除非销毁是按照初始化的反序发生).当初始化依赖于有着静态存储周期的其他变量是,坑你导致这个对象在其生命周期开始前就被访问(或在其生命周期终止后被访问.)此外,当一个程序启动结束时未合并的线城市,这些线程可能试图访问生命周期已经结束析构器已经运行的对象.

**建议:**

#### 关于析构的建议

当析构器比较琐碎时,这些析构器的执行完全不受顺序限制(它们实际上是不"运行"的).否则我们就会面临在对象的生命周期结束后访问对象的风险.因此我们只允许可平凡析构的有静态存储周期的对象.基础类型(例如指针和`int`)是平凡析构的,而可平凡析构类型的数组也是一样.注意标明`constexpr`的变量也是可平凡析构的.

```C++
const int kNum = 10;  // allowed

struct X { int n; };
const X kX[] = {{1}, {2}, {3}};  // allowed

void foo() {
  static const char* const kMessages[] = {"hello", "world"};  // allowed
}

// allowed: constexpr guarantees trivial destructor
constexpr std::array<int, 3> kArray = {{1, 2, 3}};
```

```C++
// bad: non-trivial destructor
const std::string kFoo = "foo";

// bad for the same reason, even though kBar is a reference (the
// rule also applies to lifetime-extended temporary objects)
const std::string& kBar = StrCat("a", "b", "c");

void bar() {
  // bad: non-trivial destructor
  static std::map<int, int> kData = {{1, 0}, {2, 0}, {3, 0}};
}
```

注意引用不是对象,因此他们不收析构性限制.但是动态初始化的限制仍然存在特卫视一个形如`staticT& t = *new T`的函数局部静态引用是允许的.

#### 关于初始化的建议

初始化是一个更复杂的话题.因为我们不惜不只是考虑到一个类构造器是否执行,也需要考虑初始化器的执行:

```C++
int n = 5;    // fine
int m = f();  // ? (depends on f)
Foo x;        // ? (depends on Foo::Foo)
Bar y = g();  // ? (depends on g and on Bar::Bar)
```
除了首条语句以外,我们都面临着不确定的初始化顺序.

我们所寻求的概念在正式的C++语言标准中称为*常量初始化*.这意味着初始化表达式是一个常量表达式,而且如果对象是通过构造器调用初始化的,那么构造器也必须指定为`constexpr`:

```C++
struct Foo { constexpr Foo(int) {} };

int n = 5;  // fine, 5 is a constant expression
Foo x(2);   // fine, 2 is a constant expression and the chosen constructor is constexpr
Foo a[] = { Foo(1), Foo(2), Foo(3) };  // fine
```

常量初始化总是允许的.有静态存储周期的变量的常量初始化应该被标为`constexpr`或者在有`[ABSL_CONST_INIT](https://github.com/abseil/abseil-cpp/blob/03c1513538584f4a04d666be5eb469e3979febba/absl/base/attributes.h#L540)`属性的地方.任何未按上述要求标明的非局部的静态存储周期变量都应假定为动态初始化,并且仔细检查.

相比之下呢,下述初始化是有问题的:

```C++
// Some declarations used below.
time_t time(time_t*);      // not constexpr!
int f();                   // not constexpr!
struct Bar { Bar() {} };

// Problematic initializations.
time_t m = time(nullptr);  // initializing expression not a constant expression
Foo y(f());                // ditto
Bar b;                     // chosen constructor Bar::Bar() not constexpr
```

不鼓励使用非局部变量的动态初始化,通常情况下是禁止这样的.但我们也在程序的任一方面都不依赖于此初始化相对于其他初始化的顺序时允许这种行为.在这些限制条件下,初始化的顺序不会有明显差别.例如:

```C++
int p = getpid();  // allowed, as long as no other static variable
                   // uses p in its own initialization
```

静态局部变量的动态初始化是允许(且普遍的).

#### 普遍模式

+ 全局字符串: 如果你需要一个全局或静态字符串常量,考虑使用简单字符串数组或者指向字符串文本首字符元素的指针.字符串文本具有静态存储周期已经也通常来说足够了.
+ 映射,集合和其他动态容器: 如果你需要一个静态的,固定的容器,例如一个一个用于搜索或查找表的集合,则不能(cannot)使用标准库中的动态容器作为静态变量.因为他们有非平凡析构器.相对的,考虑平凡类型的简单数组例如一个整数数组的数组(对于整数到整数的映射).或者一个`pair`对的数组(例如由`int`和`const char*`的对组成).对于小容器,线性搜索效率已经足够(而且由于内存局部性的原因,效率很高).请考虑使用[absl/algorithm/container.h](https://github.com/abseil/abseil-cpp/blob/master/absl/algorithm/container.h)中的工具用于标准操作.如果必要的话,保持容器有序并使用二分搜索.如果你真的需要使用标准库中的动态容器.考虑使用函数局部景泰直镇,如下文所述.
+ 智能指针(`unique_ptr`和`shared_ptr`): 智能指针在析构期间执行清理故而禁止使用.考虑你使用的清晰是否符合本节所属的其它模式.一个简单的解决方案是使用指向动态分配对象的普通指针并且永不删除它.(参见后述条目).
+ 自定义类型的静态变量: 如果你需要静态的某一个你需要自定义的类的常量数据,为该类添加一个平凡析构器和`constexpr`构造器.
+ 如果所有其他方法都不行,那么你可以通过使用函数局部静态指针或引用(例如`static const auto& impl = *new T(args...);`)来动态创建一个对象并且从不删除它.

### 3.6. 线程局部变量

未在函数内声明的`thread_local`线程局部变量必须(must)用真正的编译时常量初始化,并且这必须(must)使用[ABSL_CONST_INIT](https://github.com/abseil/abseil-cpp/blob/master/absl/base/attributes.h)属性来强制执行.和其他定义线程局部数据的方式相比,`thread_local`的方式更可取.

**定义:**

从C++11开始,变量可以通过`thread_local`标识符声明:

```C++
thread_local Foo foo = ...;
```

这样的变量实际上是对象的集合,因此当不同的线程访问时,线程实际上访问的是不同的对象.从很多方面来看`thread_local`变量更像是[静态存储周期变量](https://google.github.io/styleguide/cppguide.html#Static_and_Global_Variables).例如这些变量可以在命名空间中,在函数内部或者作为静态类成员来声明但是不能作为普通类成员.

**优点:**

+ 线程局部数据天生不会发生竞争(因为通常只有一个线程可以访问)这使得`thread_local`对于并发编程很有用.
+ `thread_local`是唯一受标准支持的创建线程局部数据的方式.

**缺点:**

+ 访问`thread_local`变量可能会触发执行不可预测和不可控数量的其他代码.
+ `thread_local`变量实际上是全局变量,除了缺少线程安全性以外也有所有其他全局变量的缺点.
+ 一个`thread_local`边浪的内存消耗随着运行的线程数量增长而增长(在最坏情况下),而线程数可能在程序中非常大.
+ 普通类成员不可以是`thread_local`的
+ `thread_local`可能不如某些编译器故有函数有效.

**建议:**

函数内部的`thread_local`变量没有安全担忧,因此可以随意使用.注意你可以通弄个定义一个函数或静态方法来暴露`thread_local`以使用函数作用域内的`thread_local`来模拟类内或者命名空间作用域内的`thread_local`:

```C++
Foo& MyThreadLocalFoo() {
  thread_local Foo result = ComplicatedInitialization();
  return result;
}
```

类捏或命名空间作用域内的`thread_local`变量必须要通过真正的编译时常量初始化(也就是必须是非动态初始化的).为了确保这一点,类内或命名空间内`thread_local`变量必须注明[ABSL_CONST_INIT](https://github.com/abseil/abseil-cpp/blob/master/absl/base/attributes.h)(或者也可以用`constexpr`,但应尽量少这样用):

```c++
ABSL_CONST_INIT thread_local Foo foo = ...;
```

相比于其他定义线程本地数据的机制,应优先使用`thread_local`.

## 4. 类

类是C++代码的基本单元.自然我们广泛的使用它们.本节列出了便携类是你应该遵循的主要注意事项.

### 4.1. 在构造器中完成工作

避免在够在其中调用虚方法,并且避免使用你无法在出错时指明错误的初始化方法.

**TODO**










===================================================

==翻译工作区:==

**定义:**



**优点:**



**缺点:**



**建议:**

