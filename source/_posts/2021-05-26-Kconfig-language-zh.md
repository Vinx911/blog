---
title: Kconfig-language翻译
date: 2021-05-26 18:58:22
tags: 构建工具
categories: 构建工具
copyright: true
---

# Kconfig-language翻译

## 1. 介绍

配置数据库是按树结构组织的配置选项的集合：

```
+- Code maturity level options
|  +- Prompt for development and/or incomplete code/drivers
+- General setup
|  +- Networking support
|  +- System V IPC
|  +- BSD Process Accounting
|  +- Sysctl support
+- Loadable module support
|  +- Enable loadable module support
|     +- Set version information on all module symbols
|     +- Kernel module loader
+- ...
```

每个条目都有自己的依赖性。 这些依赖关系用于确定条目的可见性。 任何子条目只有在其父条目也可见的情况下才可见。



## 2. 菜单项

大多数条目定义了一个配置选项。 所有其他条目有助于组织它们。 单个配置选项的定义如下：

```
config MODVERSIONS
	bool "Set version information on all module symbols"
	depends on MODULES
	help
	  Usually, modules have to be recompiled whenever you switch to a new kernel.  ...
```

每行以一个关键字开头，并且可以跟随多个参数。`"config"`启动一个新的配置条目。 以下几行定义了此配置选项的属性。 属性可以是`config`选项的类型，输入提示，依赖项，帮助文本和默认值。 可以使用相同的名称多次定义`config`选项，但是每个定义只能有一个输入提示，并且类型不能冲突。



## 3. 菜单属性

菜单项可以具有许多属性。 并非所有这些方法都适用于所有地方（请参见语法）。

### 3.1 类型定义

```
"bool"/"tristate"/"string"/"hex"/"int"
```

每个配置选项都必须具有一个类型。 只有两种基本类型：`tristate`和`string`； 其他类型则基于这两种。 类型定义可以选择接受输入提示，因此这两个示例是等效的：

```
	bool "Networking support"
```
和
```
	bool
	prompt "Networking support"
```

### 3.2 输入提示

```
"prompt" <prompt> ["if" <expr>]
```

每个菜单项最多可以有一个提示，用于向用户显示。可以使用`"if"`来表示输入提示的依赖性，这个依赖性是可选的。

### 3.3 默认值

```
"default" <expr> ["if" <expr>]
```

一个配置选项可以有任意多个默认值。如果存在多个默认值，只有第一个定义的默认值才是有效的。默认值可以定义在菜单入口的任何位置。这就意味着默认值可以在配置选项的其它地方再定义或者被之前的默认值定义给覆盖。 

 如果用户未设置其他值（通过上面的输入提示），则仅将默认值分配给配置符号。如果输入提示可见，则默认值将显示给用户，并且可以被用户覆盖。

 与输入提示一样，可以使用`"if"`来表示默认值的依赖性，这个依赖性是可选的。

默认值故意默认为`'n'`，以避免过大的构建。 除少数例外，新的配置选项不应更改此设置。 目的是使“ make oldconfig”在发行版本之间尽可能少地添加到配置中。

> 笔记：
>
> `"default y/m"`应该包括：
>
> a）用于始终构建的某些东西的新Kconfig选项应为`"default y"`。
>
> b）隐藏/显示其他Kconfig选项（但不生成其自身任何代码）的新Kconfig选项应为`"default y"`，以便人们看到这些其他选项。
>
> c）子驱动程序行为或`"default n"`驱动程序的类似选项。 这使您可以提供合理的默认值。
>
> d）每个人都期望的硬件或基础结构是极少数的例外，例如CONFIG_NET或CONFIG_BLOCK。 

### 3.4 类型定义+默认值

```
"def_bool"/"def_tristate" <expr> ["if" <expr>]
```

这是类型定义加值的简写形式。

可以使用`"if"`来表示默认值的依赖性，这个依赖性是可选的。

### 3.5 依赖

```
"depends on" <expr>
```

定义了此菜单项的依赖性。 如果定义了多个依赖关系，它们将与`'&&'`连接。 依赖项将应用于此菜单项中的所有其他选项（也接受`"if"`”表达式），因此这两个示例是等效的：

```
	bool "foo" if BAR
	default y if BAR
```
和
```
	depends on BAR
	bool "foo"
	default y
```

### 3.6 反向依赖

```
"select" <symbol> ["if" <expr>]
```

虽然正常依赖关系会降低符号的上限（请参见下文），但反向依赖关系可用于强制另一个符号的下限。 当前菜单符号的值用作最小值`<symbol>`可以设置为的值。 如果多次选择`<symbol>`，则将限制设置为最大选择。

反向依赖性只能与bool或tristate符号一起使用。

> 笔记：
>
> - select应谨慎使用。 select将强制符号为一个值，而无需访问依赖项。
> - 通过滥用选择，即使FOO取决于未设置的BAR，您也可以选择符号FOO。
> - 通常，仅对不可见的符号（无提示）和没有依赖性的符号选择。
> - 这将限制实用性，但另一方面，要避免所有非法配置。

### 3.7 弱反向依赖

```
"imply" <symbol> ["if" <expr>]
```

这类似于`"select"`，因为它对另一个符号施加了下限，只是`"implied"`符号的值仍可以根据直接依赖项或带有可见提示而设置为n。

给出以下示例：

```
config FOO
	tristate
	imply BAZ

  config BAZ
	tristate
	depends on BAR
```

可能是以下值：

|FOO|BAR|BAZ's default|choice for BAZ|
| :--: | :--: | :--: | :--: |
|n|y|n|N/m/y|
|m|y|m|M/y/n|
|y|y|y|Y/n|
|y|n|\*|N|

这很有用，例如：具有多个驱动程序，这些驱动程序希望表明它们具有挂钩到辅助子系统的能力，同时允许用户配置该子系统而不必取消设置这些驱动程序。

### 3.8 限制菜单显示

```
"visible if" <expr>
```

此属性仅适用于菜单块，如果条件为false，则不会向用户显示菜单块（尽管其中包含的符号仍可以由其他符号选择）。 它类似于单个菜单项的条件`"prompt"`属性。 默认值`"visible"`为true。

### 3.9 数值范围

```
"range" <symbol> <symbol> ["if" <expr>]
```

允许限制int和hex符号的可能输入值的范围。 用户只能输入一个大于或等于第一个符号且小于或等于第二个符号的值。

### 3.10 帮助文本

```
"help" or "---help---"
```

定义了一个帮助文本。 帮助文本的结尾由缩进级别决定，这意味着它在缩进程度比帮助文本第一行小的第一行处结束。

`"---help---"`和`"help"`的行为没有区别，`"---help---"`用于帮助从文件中直观地将配置逻辑与帮助中的逻辑分开，以帮助开发人员。

### 3.11 杂项选项

可以通过此选项语法定义各种不太常用的选项，这些选项可以修改菜单项及其配置符号的行为。 这些选项目前是可能的：

#### 3.11.1 defconfig_list

```
"defconfig_list"
```

这声明了默认条目的列表，这些列表可以在查找默认配置时使用（当主.config尚不存在时使用）。

#### 3.11.2 modules

```
"modules"
```

这声明该符号将用作MODULES符号，从而为所有配置符号启用第三个模块化状态。 一个符号最多可以设置“模块”选项。

#### 3.11.3 allnoconfig_y

```
"allnoconfig_y"
```

这声明使用`"allnoconfig"`时该符号应为y值。 用于隐藏其他符号的符号。



## 4. 菜单依赖性

依赖关系定义菜单项的可见性，并且还可以减小tristate符号的输入范围。 表达式中使用的tristate逻辑比普通bool逻辑多使用一种状态来表示模块状态。 依赖项表达式具有以下语法：

```
<expr> ::= <symbol>                             (1)
           <symbol> '=' <symbol>                (2)
           <symbol> '!=' <symbol>               (3)
           <symbol1> '<' <symbol2>              (4)
           <symbol1> '>' <symbol2>              (4)
           <symbol1> '<=' <symbol2>             (4)
           <symbol1> '>=' <symbol2>             (4)
           '(' <expr> ')'                       (5)
           '!' <expr>                           (6)
           <expr> '&&' <expr>                   (7)
           <expr> '||' <expr>                   (8)
```

表达式以降序排列。

1. 将符号转换为表达式。 bool和tristate符号可以简单地转换为相应的表达式值。 所有其他符号类型导致结果为`'n'`。
2. 如果两个符号的值相等，则返回`'y'`，否则返回`'n'`。
3. 如果两个符号的值相等，则返回`'n'`，否则返回`'y'`。
4. 如果`<symbol1>`的值分别小于，小于，等于或大于等于`<symbol2>`的值，则返回`'y'`，否则返回`'n'`。
5. 返回表达式的值。 用于覆盖优先级。
6. 返回`(2-/expr/)`的结果。
7. 返回`min(/expr/, /expr/)`的结果。
8. 返回`max(/expr/, /expr/)`的结果。

表达式的值可以为`'n'`，`'m'`或`'y'`（或分别为0、1、2进行计算）。 当菜单项的表达式的值为`'m'`或`'y'`时，该菜单项将变为可见。

符号有两种类型：常数符号和非常数符号。

- 非常数符号是最常见的符号，并使用`'config'`语句定义。 非常数符号完全由字母数字字符或下划线组成。
- 常数符号只是表达式的一部分。 常数符号始终用单引号或双引号引起来。 在引号内，允许使用任何其他字符，并且可以使用“ \”对引号进行转义。



## 5. 菜单结构

菜单项在树中的位置可以通过两种方式确定。 首先，可以明确指定它：

```
menu "Network device support"
	depends on NET

config NETDEVICES
	...

endmenu
```

`"menu" ... "endmenu"`块中的所有条目均成为`"Network device support"`的子菜单。 所有子条目都从菜单条目继承相关性，例如：这意味着将依赖项`NET`添加到配置选项`NETDEVICES`的依赖项列表中。

生成菜单结构的另一种方法是通过分析相关性来完成。 如果菜单项某种程度上取决于上一个条目，则可以将其作为子菜单。 首先，前一个（父）符号必须是依赖项列表的一部分，然后这两个条件之一必须为真：

- 如果父项设置为`'n'`，则子项必须不可见
- 仅当父项可见时，子项才必须可见

```
config MODULES
	bool "Enable loadable module support"

config MODVERSIONS
	bool "Set version information on all module symbols"
	depends on MODULES

comment "module support disabled"
	depends on !MODULES
```

`MODVERSIONS`直接取决于`MODULES`，这意味着仅当`MODULES`与`'n'`不同时，它才可见。 另一方面，仅当`MODULES`设置为`'n'`时，该注释才可见。



## 6. Kconfig语法

配置文件描述了一系列菜单项，其中每行以关键字开头（帮助文本除外）。 以下关键字结束菜单项：

```
- config
- menuconfig
- choice/endchoice
- comment
- menu/endmenu
- if/endif
- source
```

前五个可以用来定义菜单项。

### 6.1 config

```
config:
	"config" <symbol>
	<config options>
```

定义了一个配置符号`<symbol>`，并接受上述任何属性作为选项。

### 6.2 menuconfig

```
menuconfig:
	"menuconfig" <symbol>
	<config options>
```

类似于上面的简单配置条目，但是它给了前端一个提示，所有子选项都应显示为单独的选项列表。 为了确保所有子选项确实显示在`menuconfig`条目下而不是在菜单外部，`<config options>`列表中的每个项目都必须取决于`menuconfig`符号。
实际上，这是通过使用以下两种构造之一来实现的：

(1):

```
menuconfig M
if M
    config C1
    config C2
endif
```

(2):

```
menuconfig M
config C1
    depends on M
config C2
    depends on M
```

在下面的示例（3）和（4）中，C1和C2仍然具有M依赖性，但不再出现在`menuconfig M`下，因为
的C0，不依赖于M：

(3):

```
menuconfig M
    config C0
if M
    config C1
    config C2
endif
```

(4):

```
menuconfig M
config C0
config C1
    depends on M
config C2
    depends on M
```



### 6.3 choices

```
choices:

	"choice" [symbol]
	<choice options>
	<choice block>
	"endchoice"
```

定义了一个选择组，并接受以上任何属性作为选项。 选择只能是`bool`类型或`tristate`类型。 如果没有为选择指定任何类型，则其类型将由组中第一个选择元素的类型确定；如果没有选择元素也未指定类型，则其类型将保持未知。

`bool`类型选择仅允许选择一个配置条目，`tristate`类型选择允许将任意数量的配置条目设置为`'m'`。 如果存在用于单个硬件的多个驱动程序，并且只能将一个驱动程序编译/加载到内核中，但是可以将所有驱动程序编译为模块，则可以使用此方法。

选项接受另一个选项`"optional"`，该选项允许将选项设置为`'n'`，并且不需要选择任何条目。如果没有任何`[symbol]`与一个选项相关联，则您不能具有该选项的多个定义。 如果将`[symbol]`与该选项相关联，则可以在另一个位置定义相同的选项（即具有相同的条目）。

### 6.4 comment

```
comment:

	"comment" <prompt>
	<comment options>
```

定义了注释，该注释在配置过程中显示给用户，并且也回显到输出文件中。 其选项只能是依赖项。



### 6.5 menu

```
menu:

	"menu" <prompt>
	<menu options>
	<menu block>
	"endmenu"
```

定义了一个菜单块，有关更多信息，请参见上面的“菜单结构”。其选项只能是依赖项和`"visible"`属性。

### 6.6 if

```
if:

	"if" <expr>
	<if block>
	"endif"
```

定义了一个if块。 依赖项表达式`<expr>`附加到所有包含的菜单项中。



### 6.7 source

```
source:

	"source" <prompt>
```

这将读取指定的配置文件。 该文件总是被解析。

### 6.8 mainmenu

```
mainmenu:

	"mainmenu" <prompt>
```

如果配置程序使用`mainmenu`，将设置配置程序的标题栏。 应将其放在其他任何语句之前的配置顶部。

### 6.9 注释

```
'#' Kconfig source file comment:
```

源文件行中任何地方未加引号的'＃'字符表示源文件注释的开头。 该行的其余部分为注释。



## 7. Kconfig提示

这是Kconfig技巧的集合，乍一看大部分都不是很明显，并且大多数已经成为几个Kconfig文件中的惯用法。

### 7.1 添加常用功能并配置用法

实现与某些架构相关但并非全部与之相关的特征/功能是一种常见的习惯用法。推荐的方法是使用一个名为HAVE_ *的配置变量，这个变量被定义在一些常见的Kconfig文件中，并且被一些相关的架构文件选择。

一个典型的例子是通用IOMAP功能的实现：

我们将在`lib/Kconfig`中看到：

```
# Generic IOMAP is used to ...
config HAVE_GENERIC_IOMAP

config GENERIC_IOMAP
	depends on HAVE_GENERIC_IOMAP && FOO
```

在`lib/Makefile`中，我们将看到：

```
obj-$(CONFIG_GENERIC_IOMAP) += iomap.o
```

在每一个使用通用IOMAP功能的架构文件中我们将会看到类似于下面的配置选项：

```
config X86
	select ...
	select HAVE_GENERIC_IOMAP
	select ...
```

注意：我们使用现有的config选项，并避免创建新的config变量来选择HAVE_GENERIC_IOMAP。

注意：使用内部配置变量HAVE_GENERIC_IOMAP是为了克服select的局限性，无论依赖项如何，select都会强制将config选项设置为`'y'`。依赖关系已移至符号GENERIC_IOMAP，我们避免了select强制符号等于`'y'`的情况。

### 7.2 添加需要编译器支持的功能

有几个功能需要编译器支持。 描述对编译器功能的依赖性的推荐方法是使用`"depends on"`，后跟一个测试宏。

```
config STACKPROTECTOR
	bool "Stack Protector buffer overflow detection"
	depends on $(cc-option,-fstack-protector)
	...
```

如果需要向makefile 和/或 C源文件公开编译器功能，建议使用CC_HAS_作为config选项的前缀。

```
config CC_HAS_STACKPROTECTOR_NONE
	def_bool $(cc-option,-fno-stack-protector)
```



### 7.3 仅作为模块构建

要将组件构建限制为仅模块构建，请使用`"depends on m"`来限定其配置符号。 例如：

```
config FOO
	depends on BAR && m
```

限制 FOO 为一个模块 (=m) 或者不选择 (=n)

## 8. .......

其余内容参照kconfig-language.txt