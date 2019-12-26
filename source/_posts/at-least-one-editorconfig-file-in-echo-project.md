---
uuid: a46f5720-2590-11ea-8629-bbf8ca17b832
title: 每个项目里至少得有一份 .editorconfig 文件
s: at-least-one-editorconfig-file-in-echo-project
date: 2019-12-23 22:29:35
tags: Code Style
categories:
   - Technology
coauthor: liupeixin
---
#### 作用

计算机发展史不过几十载光阴，却以迅猛的速度渗透到社会、生活的方方面面。随着计算机软件复杂度的提升，多人协作已成为常态。多个人员共同维护一份项目，带来生产力的提升，也随之而来很多协作的问题。因此有许多方法、工具应运而生。比如 Scrum, Code Lint, Code Review, Pair Programming 等。



不同的人，使用不同的编辑器，在不同的操作系统工作，怎么保证文件编码，代码基础规范的一致性呢？解决方法就是 EditorConfig。 它是Code Lint，乃至一切文本文件协作的的基础。



现在开源项目，常会在根目录下有个 `.editorconfig`  文件， 它的 Icon 呢，是个戴着眼镜的小老鼠，这就是 EditorConfig 的配置文件。



EditorConfig 掌握起来非常简单，[官方](https://editorconfig.org/)定义：

> EditorConfig helps maintain consistent coding styles for multiple developers working on the same project across various editors and IDEs. The EditorConfig project consists of **a file format** for defining coding styles and a collection of **text editor plugins** that enable editors to read the file format and adhere to defined styles. EditorConfig files are easily readable and they work nicely with version control systems.



#### 示例

先看个简单的例子
<!-- more -->

```ini
root = true

[*]
end_of_line = lf
indent_style = space
indent_size = 2
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
max_line_length = 120
```



这个配置的意思是，该项目里的 **所有** 文件遵循以下规则

- 使用 Unix 风格换行符
- 使用 空格 缩进
- 缩进宽度为 2
- 文件编码编码字符集 UTF-8
- 删除行尾空格
- 最后一行插入空行
- 最大行宽为 120

把这个文件命名为 `.editorconfig` 保存在根目录，大多数编辑器都会去适配这个规则，从而达到每个协作者代码格式的统一。



#### 配置项列表

| 支持的配置 | 说明 | 可选值 |
| ---- | :----| ---- |
|indent_style|缩进方式| tab, space |
|indent_size|缩进大小| 数字 |
|tab_width|indent_typle=tab 时, tab宽度，如果不设置取indent_size，一般不单独设置| 数字 |
|end_of_line|换行符类型| lf, cr, crlf |
|charset|编码格式| latin1, utf-8, utf-8-bom, utf-16be, utf-16le |
|trim_trailing_whitespace|是否删除行尾空格| true, false |
|insert_final_newline|文件是否以空行结束| true, false |
|root|true, false| true, false |
|max_line_length|最大行宽(不是所有编辑器都支持)| 数字, off |

编辑器支持情况见 [这里](https://github.com/editorconfig/editorconfig/wiki/EditorConfig-Properties#widely-supported-by-editors)



#### Section 通配符


| 通配符 | 说明 | 举例 |
| ---- | :----| ---- |
|\*| 匹配除路径分隔符(/)之外的任何字符串 | [*.js] |
|\*\*| 匹配任何字符串 | [lib/**.js] |
|?| 匹配任意单个字符 | [*.s?ss] |
|\[name\]| 匹配name里的任何单个字符 | [*.[jt]s] |
|\[\!name\]| 不匹配name里的任何单个字符 | [*.[!jt]s] |
|\{s1,s2,s3\}| 匹配给定的字符串(逗号分隔) | [{*.svg, ssh.cfg}] |
|\{num1..num2\}| 匹配任何num1到num2之间的整数 | [{1..3}.txt] |



#### 编辑器支持

##### 默认支持

- BBEdit
- GitHub
- Intellij IDEA
- PyCharm
- WebStorm
- VisualStudio


##### 需要插件

- Vim

- Emacs

- Atom

- Sublime Text

- Visual Studio Code

- XCode



***以上都可以从 [官网](https://editorconfig.org/) 或者 [EditorConfig Specification](https://editorconfig-specification.readthedocs.io/en/latest/) 获得更完整详细的信息***



#### 说明

- EditorConfig 只是一个规则，具体实现来自各个编辑器
- 编辑器的 (auto)format 功能，使用该规则
- `.editorconfig` 规则 高于 编辑器规则
- 某配置 = unset 视为不设置
- EditorConfig 从上到下读取配置规则，后出现的规则覆盖之前的
- 打开一个文件，则在该文件目录及父目录寻找 `.editorconfig`  文件，直到找到 `root = true` 或者 项目根目录
- 可以为项目不同的目录配置不同的 EditorConfig 文件
- EditorConfig 只是基本的文件格式规则，更具体严格的还得依赖其他工具。比如 [ESLint](https://eslint.org/), [Pylint](https://www.pylint.org/) 等。



#### 实践

说了这么多，如果我们是个 Python + JavaScript 的项目，下面这样就够了。

```ini
root = true

[*]
end_of_line = lf
indent_style = space
indent_size = 2
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
max_line_length = 120

[*.py]
indent_size = 4

[*.json]
insert_final_newline = false

[*.{md, markdown}]
insert_final_newline = false
trim_trailing_whitespace = false
max_line_length = 0

# ignore
[{*.svg, ssh.cfg}]
end_of_line = unset
indent_style = unset
indent_size = unset
charset = unset
trim_trailing_whitespace = unset
insert_final_newline = unset
max_line_length = unset
```



比如根目录已经有`.editorconfig`文件，某子目录`.editorconfig`内容如下

```ini
root = true

[*]
```
则认为该目录忽略一切 EditorConfig 规则



全文完
