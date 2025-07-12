---
title: clang-format
description: clang-format
date: 2023-10-31
weight: 10


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="clang-format参数详解" color="secondary">}}

{{< /alert >}}

## 参考
- [clang-format演示](https://clang-format.netlify.app/emptylinebeforeaccessmodifier/)
- [clang-format](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)
- [详解 clang-format 配置选项](https://blog.csdn.net/weixin_44488341/article/details/130864934)
- [配置详解](https://www.popnic.cn/article/275522)
- `.clang-format`文件使用 `YAML` 格式，大小写敏感
- 获取特定预定义样式的所有配置选项的配置文件：`clang-format -style=llvm -dump-config > .clang-format`


- [LLVM](https://llvm.org/docs/CodingStandards.html)
- [Google](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)
- [Chromium](http://www.chromium.org/developers/coding-style)
- [Mozilla](https://developer.mozilla.org/en-US/docs/Developer_Guide/Coding_Style)
- [WebKit](https://www.webkit.org/code-style-guidelines/)


## 选项

| 选项                                        | 功能                                | 示例                                                                         |
|:--------------------------------------------|:------------------------------------|:-----------------------------------------------------------------------------|
| `BasedOnStyle`                              | 基础样式                            | `LLVM`,`Google`,`Chromium`,`Mozilla`,`WebKit`,`GNU`,`Microsoft`              |
| `AccessModifierOffset`                      | `public、private`等的缩进,建议-2     | `[-IndentWidth,0)`                                                           |
| `AlignAfterOpenBracket`                     | 开括号(圆尖方括号)后的对齐          | `Align`,`DontAlign`,`AlwaysBreak`,`BlockIndext`                              |
| `AlignArrayOfStructures`                    | 对齐结构体数组                      | 左对齐列`Left`,右对齐列`Right`,不用对齐`None`                                |
| `AlignConsecutiveAssignments`               | 对齐连续赋值                        | `Enabled`,`AcrossEmptyLines`,`AcrossComments`,`AlignCompound`,`PadOperators` |
| `AlignConsecutiveBitFields`                 | 对齐位段，针对结构体                 | 同上，不要跨过空白行                                                          |
| `AlignConsecutiveDeclarations`              | 对齐连续声明语句的变量名称          | 同上，不要跨过空白行                                                          |
| `AlignConsecutiveMacros`                    | 对齐连续宏定义                      | 同上，不要跨过空白行                                                          |
| `AlignConsecutiveShortCaseStatements`       |                                     |                                                                              |
| `AlignEscapedNewlines`                      | 对齐分割语法行的反斜杠符            | `DontAlign,Left,Right`                                                       |
| `AlignOperands`                             | 竖直对齐表达式的操作数              | `DontAlign,Align,AlignAfterOperator`，还与`BreakBeforeBinaryOperators`有关    |
| `AlignTrailingComments`                     | 对齐尾部注释                        | `Kind:Never/Always,OverEmptyLines`                                           |
| `AllowAllArgumentsOnNextLine`               | 是否允许参数在下一行上              | 函数调用或带括号的初始化列表                                                 |
| `AllowAllParametersOfDeclarationOnNextLine` | 是否允许函数声明的参数在下一行上    | 建议`false`                                                                  |
| `AllowShortBlocksOnASingleLine`             | 是否允许短语法块在单行              | `Never,Empty,Always`,建议`Never`                                             |
| `AllowShortCaseLabelsOnASingleLine`         | 是否允许将短的case语句在单行        | 建议`false`                                                                  |
| `AllowShortEnumsOnASingleLine`              | 是否允许短枚举在单行                | 建议`false`                                                                  |
| `AllowShortFunctionsOnASingleLine`          | 是否允许短函数在单行                | `None,InlineOnly,Empty,Inline,ALL`,建议`None`                                |
| `AllowShortIfStatementsOnASingleLine`       | 是否允许if块在单行                  | `Never,WithoutElse,OnlyFirstIf,AllIfsAndElse`                                |
| `AllowShortLambdasOnASingleLine`            | 是否允许短匿Lambda函数在单行        | `None,Empty,Inline,All`                                                      |
| `AllowShortLoopsOnASingleLine`              | 是否允许合并短循环到单行            | 建议`false`                                                                  |
| `AlwaysBreakAfterDefinitionReturnType`      | 已经弃用该选项                      |                                                                              |
| `AlwaysBreakAfterReturnType`                | 已经弃用该选项                      | `BreakAfterReturnType`                                                       |
| `AlwaysBreakBeforeMultilineStrings`         | 多行字符串断行                      | 建议`false`                                                                  |
| `AlwaysBreakTemplateDeclarations`           | 已经弃用该选项                      | `BreakTemplateDeclarations`                                                  |
| `AttributeMacros`                           |                                     |                                                                              |
| `BinPackArguments`                          | 函数调用变量同一行还是单行          | 建议`false`                                                                  |
| `BinPackParameters`                         | 函数声明或定义参数                  | 建议`false`                                                                  |
| `BitFieldColonSpacing`                      | 位段列的空白风格                    | `None,Both,Before,After`                                                     |
| `BraceWrapping`                             | 大括号换行风格                      | `BreakBeforeBraces: Custom`                                                  |
| `BreakBeforeBraces`                         |                                     |                                                                              |
| `BreakAfterAttributes`                      |                                     |                                                                              |
| `BreakAfterJavaFieldAnnotations`            |                                     |                                                                              |
| `BreakArrays`                               |                                     |                                                                              |
| `BreakBeforeBinaryOperators`                |                                     |                                                                              |
| `BreakBeforeConceptDeclarations`            |                                     |                                                                              |
| `BreakBeforeInlineASMColon`                 |                                     |                                                                              |
| `BreakBeforeTernaryOperators`               |                                     |                                                                              |
| `BreakConstructorInitializers`              |                                     |                                                                              |
| `BreakInheritanceList`                      |                                     |                                                                              |
| `BreakStringLiterals`                       |                                     |                                                                              |
| `ColumnLimit`                               | 列数限制                            | 0表示不限制，尊重源文件                                                       |
| `CommentPragmas`                            |                                     |                                                                              |
| `CompactNamespaces`                         |                                     |                                                                              |
| `ConstructorInitializerIndentWidth`         |                                     |                                                                              |
| **ContinuationIndentWidth**                 | 续行缩进的宽度                      | `8`                                                                          |
| `Cpp11BracedListStyle`                      |                                     |                                                                              |
| `DerivePointerAlignment`                    | 提取指针对齐,可能影响源码           |                                                                              |
| `DisableFormat`                             | 完全禁用格式化                      | `false`                                                                      |
| `EmptyLineAfterAccessModifier`              | 访问修饰符后空行                    | `Never/Always/Leave`                                                         |
| `EmptyLineBeforeAccessModifier`             | 访问修饰符前空行                    | `LogicalBlock/Never/Always/Leave`                                            |
| `ExperimentalAutoDetectBinPacking`          | 不要用                              | `false`                                                                      |
| `FixNamespaceComments`                      | 修复命名空间描述                    | `false/true`                                                                 |
| `ForEachMacros`                             |                                     |                                                                              |
| `IfMacros`                                  |                                     |                                                                              |
| `IncludeBlocks`                             | `#include`块排序                    | `Preserve/Regroup/Merge`                                                     |
| `IncludeCategories`                         |                                     |                                                                              |
| `IncludeIsMainRegex`                        |                                     |                                                                              |
| `IncludeIsMainSourceRegex`                  |                                     |                                                                              |
| **IndentAccessModifiers**                   | 缩进访问修饰符，建议false            | `false/true`                                                                 |
| **IndentCaseBlocks**                        | 缩进case块{}，建议false              | `false/true`                                                                 |
| **IndentCaseLabels**                        | 缩进case标签，建议false              | `false/true`                                                                 |
| `IndentExternBlock`                         | 缩进Extern扩展块                    | `false/true`                                                                 |
| `IndentGotoLabels`                          | 缩进Goto                            |                                                                              |
| **IndentPPDirectives**                      | 缩进预处理指令                      | `BeforeHash/AfterHash/None`                                                  |
| `IndentRequiresClause`                      |                                     |                                                                              |
| **IndentWidth**                             | 缩进宽度,缩进的列数                 | `8/4/2`                                                                      |
| `IndentWrappedFunctionNames`                | 函数定义或声明在类型之后换缩进      |                                                                              |
| **InsertBraces**                            | 插入括号，不要设置成true             | `false/true`                                                                 |
| `InsertNewlineAtEOF`                        |                                     |                                                                              |
| `InsertTrailingCommas`                      | 只对JavaScript可用，默认禁用         |                                                                              |
| `IntegerLiteralSeparator`                   |                                     |                                                                              |
| `JavaScriptQuotes`                          |                                     |                                                                              |
| `JavaScriptWrapImports`                     |                                     |                                                                              |
| `KeepEmptyLinesAtTheStartOfBlocks`          |                                     |                                                                              |
| `KeepEmptyLinesAtEOF`                       |                                     |                                                                              |
| `LambdaBodyIndentation`                     |                                     |                                                                              |
| `LineEnding`                                |                                     |                                                                              |
| `MacroBlockBegin`                           |                                     |                                                                              |
| `MacroBlockEnd`                             |                                     |                                                                              |
| `MaxEmptyLinesToKeep`                       |                                     |                                                                              |
| `NamespaceIndentation`                      | 命名空间缩进                        | `None/All/Inner/`                                                            |
| `ObjCBinPackProtocolList`                   |                                     |                                                                              |
| `ObjCBlockIndentWidth`                      |                                     |                                                                              |
| `ObjCBreakBeforeNestedBlockParam`           |                                     |                                                                              |
| `ObjCSpaceAfterProperty`                    |                                     |                                                                              |
| `ObjCSpaceBeforeProtocolList`               |                                     |                                                                              |
| `PackConstructorInitializers`               |                                     |                                                                              |
| `PenaltyBreakAssignment`                    |                                     |                                                                              |
| `PenaltyBreakBeforeFirstCallParameter`      |                                     |                                                                              |
| `PenaltyBreakComment`                       |                                     |                                                                              |
| `PenaltyBreakFirstLessLess`                 |                                     |                                                                              |
| `PenaltyBreakOpenParenthesis`               |                                     |                                                                              |
| `PenaltyBreakString`                        |                                     |                                                                              |
| `PenaltyBreakTemplateDeclaration`           |                                     |                                                                              |
| `PenaltyExcessCharacter`                    |                                     |                                                                              |
| `PenaltyIndentedWhitespace`                 |                                     |                                                                              |
| `PenaltyReturnTypeOnItsOwnLine`             |                                     |                                                                              |
| `PointerAlignment`                          | 指针对齐风格                        | `Middle/Left/Right`                                                          |
| `PPIndentWidth`                             | 预处理符号缩进指定                  |                                                                              |
| `QualifierAlignment`                        | 限定符对齐                          | `Leave/Left/Right/Custom`                                                    |
| `RawStringFormats`                          |                                     |                                                                              |
| `ReferenceAlignment`                        |                                     |                                                                              |
| **ReflowComments**                          | 是否重排版注释，不要设置成true       | `false/true`                                                                 |
| **RemoveBracesLLVM**                        | 是否移除括号，不要设置成true         | `false/true`                                                                 |
| **RemoveParentheses**                       | 是否移除分号，不要设置成true         | `false/true`                                                                 |
| `RemoveSemicolon`                           |                                     |                                                                              |
| `RequiresClausePosition`                    |                                     |                                                                              |
| `RequiresExpressionIndentation`             |                                     |                                                                              |
| `SeparateDefinitionBlocks`                  |                                     |                                                                              |
| `ShortNamespaceLines`                       |                                     |                                                                              |
| **SkipMacroDefinitionBody**                 | 不要格式化宏定义体                  | `1/0`,版本18开始                                                             |
| **SortIncludes**                            | 排序include，要设置为Never           | `Never/CaseSensitive/CaseInsensitive`                                        |
| `SortJavaStaticImport`                      | 排序java静态导入                    | `Before/After`                                                               |
| **SortUsingDeclarations**                   | 排序using声明                       | `Never/Lexicographic/LexicographicNumeric`                                   |
| `SpaceAfterCStyleCast`                      |                                     |                                                                              |
| `SpaceAfterLogicalNot`                      |                                     |                                                                              |
| `SpaceAfterTemplateKeyword`                 |                                     |                                                                              |
| `SpaceAroundPointerQualifiers`              | 指针限定符空格,Default              | `Default/Before/After/Both`                                                  |
| `SpaceBeforeAssignmentOperators`            | 赋值操作符前是否给空格，要设置为true | `true/false`                                                                 |
| `SpaceBeforeCaseColon`                      | case前空格，设置为false              | `false/true`                                                                 |
| `SpaceBeforeCpp11BracedList`                |                                     |                                                                              |
| `SpaceBeforeCtorInitializerColon`           |                                     |                                                                              |
| `SpaceBeforeInheritanceColon`               |                                     |                                                                              |
| `SpaceBeforeJsonColon`                      |                                     |                                                                              |
| `SpaceBeforeParens`                         | 圆括号前空格                        |                                                                              |
| `SpaceBeforeParensOptions`                  |                                     |                                                                              |
| `SpaceBeforeRangeBasedForLoopColon`         |                                     |                                                                              |
| `SpaceBeforeSquareBrackets`                 |                                     |                                                                              |
| `SpaceInEmptyBlock`                         |                                     |                                                                              |
| `SpacesBeforeTrailingComments`              |                                     |                                                                              |
| `SpacesInAngles`                            |                                     |                                                                              |
| `SpacesInContainerLiterals`                 |                                     |                                                                              |
| `SpacesInLineCommentPrefix`                 |                                     |                                                                              |
| `SpacesInParens`                            |                                     |                                                                              |
| `SpacesInParensOptions`                     |                                     |                                                                              |
| `SpacesInSquareBrackets`                    |                                     |                                                                              |
| **Standard**                                | 解析和格式化与此标准兼容的c++结构   | `Auto/c++17/Latest/Auto/c++11/c++14/c++20`                                   |
| `StatementAttributeLikeMacros`              |                                     |                                                                              |
| `StatementMacros`                           |                                     |                                                                              |
| **TabWidth**                                | 制表符宽度                          | `8/4`                                                                        |
| **UseTab**                                  | 格式化是否使用制表符                | `Always,Never,AlignWithSpaces,ForContinuationAndIndentation,ForIndentation`  |
| `VerilogBreakBetweenInstancePorts`          |                                     |                                                                              |
| `WhitespaceSensitiveMacros`                 |                                     |                                                                              |



## 示例


#### 访问性修饰符

源码示例：`/root/mysql-server/client/dump/abstract_connection_provider.h`
```c++
#ifndef ABSTRACT_CONNECTION_PROVIDER_INCLUDED
#define ABSTRACT_CONNECTION_PROVIDER_INCLUDED

#include <functional>

#include "client/base/i_connection_factory.h"
#include "client/dump/i_connection_provider.h"
#include "my_inttypes.h"

namespace Mysql {
namespace Tools {
namespace Dump {

class Abstract_connection_provider : public I_connection_provider {
 protected:
  explicit Abstract_connection_provider(
      Mysql::Tools::Base::I_connection_factory *connection_factory);

  virtual Mysql::Tools::Base::Mysql_query_runner *create_new_runner(
      std::function<bool(const Mysql::Tools::Base::Message_data &)>
          *message_handler);

 private:
  Mysql::Tools::Base::I_connection_factory *m_connection_factory;

  class Message_handler_wrapper {
   public:
    explicit Message_handler_wrapper(
        std::function<bool(const Mysql::Tools::Base::Message_data &)>
            *message_handler);

    int64 pass_message(const Mysql::Tools::Base::Message_data &message);

   private:
    std::function<bool(const Mysql::Tools::Base::Message_data &)>
        *m_message_handler;
  };
};

}  // namespace Dump
}  // namespace Tools
}  // namespace Mysql

#endif
```

###### 1. `IndentAccessModifiers`
缩进访问性修饰符

- true 则表示这些`private、public、protected`关键字按照`IndentWidth`缩进
- false 则表示这些`private、public、protected`关键字与定义的class相同层级，即不缩进，但实际是否缩进，还取决于`AccessModifierOffset`

**格式化**：`IndentAccessModifiers: true`
```c++
#ifndef ABSTRACT_CONNECTION_PROVIDER_INCLUDED
#define ABSTRACT_CONNECTION_PROVIDER_INCLUDED

#include <functional>

#include "client/base/i_connection_factory.h"
#include "client/dump/i_connection_provider.h"
#include "my_inttypes.h"

namespace Mysql {
namespace Tools {
namespace Dump {

class Abstract_connection_provider : public I_connection_provider {
        protected:
                explicit Abstract_connection_provider(
                    Mysql::Tools::Base::I_connection_factory *connection_factory);

                virtual Mysql::Tools::Base::Mysql_query_runner *create_new_runner(
                    std::function<bool(const Mysql::Tools::Base::Message_data &)>
                        *message_handler);

        private:
                Mysql::Tools::Base::I_connection_factory *m_connection_factory;

                class Message_handler_wrapper {
                        public:
                                explicit Message_handler_wrapper(
                                    std::function<bool(const Mysql::Tools::Base::Message_data &)>
                                        *message_handler);

                                int64 pass_message(const Mysql::Tools::Base::Message_data &message);

                        private:
                                std::function<bool(const Mysql::Tools::Base::Message_data &)>
                                    *m_message_handler;
                };
};

}  // namespace Dump
}  // namespace Tools
}  // namespace Mysql

#endif
```
可以看出：这样缩进太大了，建议关闭

###### 2. `AccessModifierOffset`
 缩进访问性修饰符

若`IndentWidth=8`，则缩进8个空格
- 这里假设：`IndentAccessModifiers: false`
- 若`AccessModifierOffset=-4`，则表示这些`private、public、protected`关键字另起一行的在缩进8个空格后再回退4个空格，即实际上相对上一代码的缩进只有4个空格
- 若`AccessModifierOffset=-8`，则表示这些`private、public、protected`关键字另起一行的在缩进8个空格后再回退8个空格，即实际上相对上一代码的缩进只有0个空格，即没有缩进
- 因此，`AccessModifierOffset`的绝对值不要大于`IndentWidth`的值，即`[-IndentWidth,0)`
- 因此建议：`IndentWidth=8， AccessModifierOffset=-2`，这样做的原因是这样恰好与class加上一个空格的长度上，这样更美观和层次感

**格式化**：`IndentWidth=8,AccessModifierOffset=-2`
```c++
#ifndef ABSTRACT_CONNECTION_PROVIDER_INCLUDED
#define ABSTRACT_CONNECTION_PROVIDER_INCLUDED

#include <functional>

#include "client/base/i_connection_factory.h"
#include "client/dump/i_connection_provider.h"
#include "my_inttypes.h"

namespace Mysql {
namespace Tools {
namespace Dump {

class Abstract_connection_provider : public I_connection_provider {
      protected:
        explicit Abstract_connection_provider(
            Mysql::Tools::Base::I_connection_factory *connection_factory);

        virtual Mysql::Tools::Base::Mysql_query_runner *create_new_runner(
            std::function<bool(const Mysql::Tools::Base::Message_data &)>
                *message_handler);

      private:
        Mysql::Tools::Base::I_connection_factory *m_connection_factory;

        class Message_handler_wrapper {
              public:
                explicit Message_handler_wrapper(
                    std::function<bool(const Mysql::Tools::Base::Message_data &)>
                        *message_handler);

                int64 pass_message(const Mysql::Tools::Base::Message_data &message);

              private:
                std::function<bool(const Mysql::Tools::Base::Message_data &)>
                    *m_message_handler;
        };
};

}  // namespace Dump
}  // namespace Tools
}  // namespace Mysql

#endif

```


###### 3. `EmptyLineBeforeAccessModifier`
定义在什么情况下在访问修饰符之前放置空行
- Never 删除访问修饰符之前的所有空行
- Always 始终在访问修饰符之前添加空行，除非访问修饰符位于结构或类定义的开头。
- Leave
- LogicalBlock 仅当访问修饰符启动新逻辑块时添加空行。逻辑块是一组一个或多个成员字段或函数

###### 4. `EmptyLineAfterAccessModifier`
定义何时在访问修饰符后放置空行
- Never 删除访问修饰符后面的所有空行
- Always 如果没有访问修饰符，请始终在访问修饰符后添加空行。MaxEmptyLinesToKeep 也适用
- Leave 在访问修饰符之后保留现有的空行。改为应用 MaxEmptyLinesToKeep

#### 数组


###### 1. `AlignArrayOfStructures`




#### 指针引用


###### 1. `PointerAlignment`





###### 2. `ReferenceAlignment`



#### 圆括号

###### 1. `SpacesInParentheses`


#### 方括号

###### 1. `SpacesInSquareBrackets`



#### 注释

###### 1. `SpacesInLineCommentPrefix`

###### 2.`SpacesBeforeTrailingComments`

###### 3.`AlignTrailingComments`
如果为 true，则对齐尾随注释

#### 函数

###### 1. `BinPackArguments`

###### 2.`AllowAllArgumentsOnNextLine`


###### 3.`AllowAllParametersOfDeclarationOnNextLine`

如果函数声明不适合一行，则允许将函数声明的所有参数放到下一行



###### 4.`AllowShortFunctionsOnASingleLine`




#### if

###### 1. `AllowShortIfStatementsOnASingleLine`



#### lambda

###### 1. `AllowShortLambdasOnASingleLine`



#### while

###### 1. `AllowShortLoopsOnASingleLine`


#### return

###### 1. `AlwaysBreakAfterReturnType`


#### 字符串

###### 1. `AlwaysBreakBeforeMultilineStrings`


#### template

###### 1. `AlwaysBreakTemplateDeclarations`





#### 继承

###### 1. `DeriveLineEnding`

###### 2.`DerivePointerAlignment`

###### 3.`SpaceBeforeInheritanceColon`




#### 头文件

###### 1. `SortIncludes`

###### 2.`IncludeBlocks`

###### 3.`IncludeCategories`





#### 缩进

###### 1. `IndentWidth`

###### 2. `ContinuationIndentWidth`
连续行缩进宽度

###### 3.`TabWidth`

###### 4.`UseTab`


#### 宏定义


###### 1. `AlignConsecutiveMacros`

#### 换行符


###### 1. `AlignEscapedNewlines`

#### 声明


###### 1. `AlignConsecutiveDeclarations`


#### 二元和三元表达式


###### 1. `AlignOperands`

#### 赋值


###### 1. `AlignConsecutiveAssignments`
就是当连续出现多个赋值语句时，是否将等号=对齐

建议配置如下：
```yaml
AlignConsecutiveAssignments:
  Enabled:         true
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   true
  PadOperators:    true
```


#### `case`

###### 1. `IndentCaseLabels`

1. `IndentCaseLabels:	true`
```c++
static const EVP_CIPHER *aes_evp_type(const my_aes_opmode mode) {
        switch (mode) {
                case my_aes_128_ecb:
                        return EVP_aes_128_ecb();
                case my_aes_128_cbc:
                        return EVP_aes_128_cbc();
                case my_aes_128_cfb1:
                        return EVP_aes_128_cfb1();
                case my_aes_128_cfb8:
                        return EVP_aes_128_cfb8();
                case my_aes_128_cfb128:
                        return EVP_aes_128_cfb128();
                case my_aes_128_ofb:
                        return EVP_aes_128_ofb();
                case my_aes_192_ecb:
                        return EVP_aes_192_ecb();
                case my_aes_192_cbc:
                        return EVP_aes_192_cbc();
                case my_aes_192_cfb1:
                        return EVP_aes_192_cfb1();
                case my_aes_192_cfb8:
                        return EVP_aes_192_cfb8();
                case my_aes_192_cfb128:
                        return EVP_aes_192_cfb128();
                case my_aes_192_ofb:
                        return EVP_aes_192_ofb();
                case my_aes_256_ecb:
                        return EVP_aes_256_ecb();
                case my_aes_256_cbc:
                        return EVP_aes_256_cbc();
                case my_aes_256_cfb1:
                        return EVP_aes_256_cfb1();
                case my_aes_256_cfb8:
                        return EVP_aes_256_cfb8();
                case my_aes_256_cfb128:
                        return EVP_aes_256_cfb128();
                case my_aes_256_ofb:
                        return EVP_aes_256_ofb();
                default:
                        return nullptr;
        }
}

```

2. `IndentCaseLabels:	false`
```c++
static const EVP_CIPHER *aes_evp_type(const my_aes_opmode mode) {
        switch (mode) {
        case my_aes_128_ecb:
                return EVP_aes_128_ecb();
        case my_aes_128_cbc:
                return EVP_aes_128_cbc();
        case my_aes_128_cfb1:
                return EVP_aes_128_cfb1();
        case my_aes_128_cfb8:
                return EVP_aes_128_cfb8();
        case my_aes_128_cfb128:
                return EVP_aes_128_cfb128();
        case my_aes_128_ofb:
                return EVP_aes_128_ofb();
        case my_aes_192_ecb:
                return EVP_aes_192_ecb();
        case my_aes_192_cbc:
                return EVP_aes_192_cbc();
        case my_aes_192_cfb1:
                return EVP_aes_192_cfb1();
        case my_aes_192_cfb8:
                return EVP_aes_192_cfb8();
        case my_aes_192_cfb128:
                return EVP_aes_192_cfb128();
        case my_aes_192_ofb:
                return EVP_aes_192_ofb();
        case my_aes_256_ecb:
                return EVP_aes_256_ecb();
        case my_aes_256_cbc:
                return EVP_aes_256_cbc();
        case my_aes_256_cfb1:
                return EVP_aes_256_cfb1();
        case my_aes_256_cfb8:
                return EVP_aes_256_cfb8();
        case my_aes_256_cfb128:
                return EVP_aes_256_cfb128();
        case my_aes_256_ofb:
                return EVP_aes_256_ofb();
        default:
                return nullptr;
        }
}

```


###### 2. `AllowShortCaseLabelsOnASingleLine`



#### 枚举

###### 1. `AllowShortEnumsOnASingleLine`













```yaml
AlignConsecutiveAssignments:
  Enabled:         false
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   false
  PadOperators:    true
AlignConsecutiveBitFields:
  Enabled:         false
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   false
  PadOperators:    false
AlignConsecutiveDeclarations:
  Enabled:         false
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   false
  PadOperators:    false
AlignConsecutiveMacros:
  Enabled:         false
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   false
  PadOperators:    false
AlignConsecutiveShortCaseStatements:
  Enabled:         false
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCaseColons: false
```


**AlignAfterOpenBracket**示例：
> `AlignAfterOpenBracket: Align` 在左括号处对齐参数 **最美**
```c++
// AlignAfterOpenBracket: Align
// 在左括号处对齐参数
someLongFunction(argument1,
                 argument2);

// AlignAfterOpenBracket: DontAlign
// 不对齐，换行后根据参数 ContinuationIndentWidth 缩进
someLongFunction(argument1,
    argument2);

// AlignAfterOpenBracket: AlwaysBreak
// 如果参数放一行不下，则总是换行
someLongFunction(
    argument1, argument2);

// AlignAfterOpenBracket: BlockIndent
someLongFunction(
    argument1, argument2
)

```

**AlignConsecutiveAssignments**示例：
- Enabled
- AcrossEmptyLines 是否开启跨越空白行对齐
- AcrossComments 是否开启跨越注释行对齐
- AlignCompound 是否开启混合运算符与=对齐
- PadOperators 是否开启跨越空白行对齐

```yaml
AlignConsecutiveAssignments:
  Enabled:         false
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   false
  PadOperators:    true
```

- AcrossEmptyLines
```c++
// true:
int a            = 1;
int somelongname = 2;
double c         = 3;

int d            = 3;

// false:
int a            = 1;
int somelongname = 2;
double c         = 3;

int d = 3;
```

- AcrossComments
```c++
// true:
int d    = 3;
/* A comment. */
double e = 4;

// false:
int d = 3;
/* A comment. */
double e = 4;
```

- AlignFunctionPointers
```c++
//true:
unsigned i;
int     &r;
int     *p;
int      (*f)();

//false:
unsigned i;
int     &r;
int     *p;
int (*f)();
```

- PadOperators
```c++
//true:
a   >>= 2;
bbb   = 2;

a     = 2;
bbb >>= 2;

//false:
a >>= 2;
bbb = 2;

a     = 2;
bbb >>= 2;
```

**AlignConsecutiveBitFields**

```yaml
AlignConsecutiveBitFields:
  Enabled: true
  AcrossEmptyLines: true
  AcrossComments: false
```



- AllowShortFunctionsOnASingleLine
{{<alert>}}
**详解：**
- None 从不合并函数到单行
- Empty 只合并空函数
- All 合并所有合适的短函数在单行上
- InlineOnly 仅合并被定义在类里面的短函数,与Inline区别在于不含外部顶级定义的空函数
- Inline 合并被定义在类里面的短函数，以及空函数
{{</alert>}}



- BraceWrapping

```yaml
BraceWrapping:
  AfterCaseLabel:  false
  AfterClass:      false
  AfterControlStatement: Never
  AfterEnum:       false
  AfterExternBlock: false
  AfterFunction:   false
  AfterNamespace:  false
  AfterObjCDeclaration: false
  AfterStruct:     false
  AfterUnion:      false
  BeforeCatch:     false
  BeforeElse:      false
  BeforeLambdaBody: false
  BeforeWhile:     false
  IndentBraces:    false
  SplitEmptyFunction: true
  SplitEmptyRecord: true
  SplitEmptyNamespace: true
```

{{<alert>}}
**详解：**
- `BreakBeforeBraces: Custom`
- AfterClass 换行类定义,建议false
- AfterControlStatement 换行控制语句 (if/while/for/switch/...)，`Never,MultiLine,Always`，建议Never
- AfterCaseLabel 对case后面的大括号换行,建议false
- AfterEnum 建议false
- AfterNamespace 建议false
- AfterFunction 建议false
- AfterStruct 建议false
- AfterUnion 建议false
- AfterExternBlock extern声明之后换行，建议false
- BeforeCatch 在catch之前换行，建议false
- BeforeElse 建议true
- BeforeLambdaBody  在Lambda表达式块之前换行，建议false
- BeforeWhile 在while之前换行，建议true
- IndentBraces 对换行的大括号缩进
- SplitEmptyFunction
- SplitEmptyRecord
- SplitEmptyNamespace

{{</alert>}}










## 问题

#### `clang-format`格式化后，导致源码代码报错

`/root/mysql-server-8032/sql/query_options.h`
```c++
#define OPTION_FOUND_ROWS \
 57   (1ULL << 5)                             // SELECT, user

```
会在` // SELECT, user`后面添加一个反斜杠，导致编译出错，因此编译出错时，可将此反斜杠移除


源代码：`/root/mysql-server-8032/sql/mysqld.cc`
```c++

#ifndef __APPLE__  // Mac OS doesn't have sigwaitinfo.
                   //  Log a note if mysqld is restarted via kill command.
        if (sig_info.si_pid != getpid()) {
          sql_print_information(
              "Received signal SIGUSR2."
              " Restarting mysqld (Version %s)",
              server_version);
        }
#endif             // __APPLE__

```


错误格式化后
```c++

#ifndef __APPLE__ // Mac OS doesn't have sigwaitinfo. \
                  //  Log a note if mysqld is restarted via kill command.
                        if (sig_info.si_pid != getpid()) {
                                sql_print_information(
                                        "Received signal SIGUSR2."
                                        " Restarting mysqld (Version %s)",
                                        server_version);
                        }
#endif            // __APPLE__


```

**解决**：找到这种注释，删除这个结尾

1. 手动修复
```bash
find ./mysql-server-8032-format/ -name "*.cc" |xargs grep -E '//.*\\$'


find ./mysql-server-8032-format/ -name "*.h" |xargs grep -E '//.*\\$'


sed '1s/\\//' a.txt
```

2. 寻找`clang-format`格式化参数配置，避免出现这种情况












#### ABI check found difference between
> 出现这个的根本原因是：你先对源码执行了cmake3,然后又去修改源码（比如我就是用clang-format再格式化出错后，再修改），然后又来执行make编译，此时就会出现abi check问题

[ABI check](https://bugs.mysql.com/bug.php?id=61297)

1. `make abi_update`
2. cmake3后就不要再次修改源码了，要么就在cmake之前修改源码，要保证：cmake3和make两个命令之间的时间不能修改源码




如果希望某个代码段不要参与格式化，可使用 // clang-format off 和 // clang-format on 注释对该代码段进行限定，像下面这样：
```c++
void test()
{
    // clang-format off
    My::MatrixXd ref_mat = My::MatrixXd::Zero(6, 6);
    ref_mat <<
         1,        0,        0,        0,        0,        0,
         1,      0.5,     0.25,    0.125,   0.0625,  0.03125,
         1,        1,        1,        1,        1,        1,
        -1,       -0,       -0,       -0,       -0,       -0,
        -1,     -0.5,    -0.25,   -0.125,  -0.0625, -0.03125,
        -1,       -1,       -1,       -1,       -1,       -1;
    // clang-format on
}
```



`/root/mysql-server-8032-format/cmake/do_abi_check.cmake`：
```cmake
set(abi_check_out ${BINARY_DIR}/abi_check.out)

set(ABI_SOURCE_DIR ${SOURCE_DIR})
set(ABI_BINARY_DIR ${BINARY_DIR})

# If we're using WSL on Windows, we have to translate paths using wslpath
if(WSL_EXECUTABLE)
        execute_process(
                COMMAND ${WSL_EXECUTABLE} wslpath ${ABI_SOURCE_DIR}
                OUTPUT_STRIP_TRAILING_WHITESPACE
                OUTPUT_VARIABLE ABI_SOURCE_DIR)
        execute_process(
                COMMAND ${WSL_EXECUTABLE} wslpath ${ABI_BINARY_DIR}
                OUTPUT_STRIP_TRAILING_WHITESPACE
                OUTPUT_VARIABLE ABI_BINARY_DIR)
        execute_process(
                COMMAND ${WSL_EXECUTABLE} wslpath ${abi_check_out}
                OUTPUT_STRIP_TRAILING_WHITESPACE
                OUTPUT_VARIABLE abi_current)
else()
        set(abi_current ${abi_check_out})
endif()

foreach(file ${ABI_HEADERS})
        get_filename_component(header_basename ${file} NAME)
        set(tmpfile ${BINARY_DIR}/${header_basename}.pp.tmp)
        set(abi_file ${file})
        if(WSL_EXECUTABLE)
                execute_process(
                        COMMAND ${WSL_EXECUTABLE} wslpath ${abi_file}
                        OUTPUT_STRIP_TRAILING_WHITESPACE
                        OUTPUT_VARIABLE abi_file)
        endif()

        execute_process(
                COMMAND
                        ${WSL_EXECUTABLE} ${COMPILER} -E -nostdinc -dI -DMYSQL_ABI_CHECK -I${ABI_SOURCE_DIR}/include
                        -I${ABI_BINARY_DIR}/include -I${ABI_SOURCE_DIR}/include/mysql -I${ABI_SOURCE_DIR}/sql
                        -I${ABI_SOURCE_DIR}/libbinlogevents/export ${abi_file}
                ERROR_QUIET
                OUTPUT_FILE ${tmpfile})
        execute_process(
                COMMAND ${WSL_EXECUTABLE} sed -e "/^# /d" -e "/^[	]*$/d" -e "/^#pragma GCC set_debug_pwd/d" -e "/^#ident/d"
                RESULT_VARIABLE result
                OUTPUT_FILE ${abi_check_out}
                INPUT_FILE ${tmpfile})
        if(NOT ${result} EQUAL 0)
                message(FATAL_ERROR "sed returned error ${result}")
        endif()
        file(REMOVE ${tmpfile})

        if(WSL_EXECUTABLE)
                execute_process(
                        COMMAND ${WSL_EXECUTABLE} wslpath ${file}
                        OUTPUT_STRIP_TRAILING_WHITESPACE
                        OUTPUT_VARIABLE abi_original)
        else()
                set(abi_original ${file})
        endif()

        execute_process(COMMAND ${WSL_EXECUTABLE} diff -w ${abi_original}.pp ${abi_current} RESULT_VARIABLE result)
        if(NOT ${result} EQUAL 0)
                message(FATAL_ERROR "ABI check found difference between ${file}.pp and ${abi_check_out}")
        endif()
        file(REMOVE ${abi_check_out})
endforeach()

```










#### 配置文件：`clang-format`
{{<alert>}}
当使用clang-format命令行实用程序，可以使用预定义样式之一：
- LLVM
- Google
- Chromium
- Mozilla
- WebKit
- Microsoft
- GNU
- 通过配置特定样式选项来创建自定义样式

先生成谷歌风格的默认配置文件：`clang-format -style=google -dump-config > .clang-format`

然后在此基础上修改:
1. `IndentWidth`：由2改成8
2. `ColumnLimit`：由80改成128
3. `AlignConsecutiveAssignments`：中的`Enabled、AlignCompound`由`false`改成`true`
4. `SortIncludes`：由`CaseSensitive`改成`Never`，不改的话，将会重新排序头文件顺序，而MySQL源码依赖头文件顺序，因此，必须指定不能排序
5. `ShortNamespaceLines`: 由1改成0
6. `SortUsingDeclarations`: 由`LexicographicNumeric`改成`Never`


{{</alert>}}



```bash
[root@dev /root/tmp]# diff google-default.clang-format .clang-format
3c3
< # BasedOnStyle:  Google
---
> BasedOnStyle:  Google
8c8
<   Enabled:         false
---
>   Enabled:         true
11c11
<   AlignCompound:   false
---
>   AlignCompound:   true
89c89
< ColumnLimit:     80
---
> ColumnLimit:     128
134c134
< IndentWidth:     2
---
> IndentWidth:     8

```


####

```bash
[root@dev /root/mysql-server-8032-google]# diff .clang-format ../conf/google.clang-format
3c3,4
< AccessModifierOffset: -8
---
> # BasedOnStyle:  Google
> AccessModifierOffset: -1
7c8
<   Enabled:         true
---
>   Enabled:         false
10c11
<   AlignCompound:   true
---
>   AlignCompound:   false
88c89
< ColumnLimit:     0
---
> ColumnLimit:     80
128c129
< IndentCaseLabels: false
---
> IndentCaseLabels: true
133c134
< IndentWidth:     8
---
> IndentWidth:     2
213c214
< SortIncludes:    false
---
> SortIncludes:    CaseSensitive
215c216
< SortUsingDeclarations: Never
---
> SortUsingDeclarations: LexicographicNumeric

```


```yaml
---
BasedOnStyle: Google
AccessModifierOffset: -8
AlignConsecutiveAssignments:
  Enabled:         true
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   true
  PadOperators:    true
ColumnLimit:     128
IndentCaseLabels: false
IndentWidth: 8
SortIncludes:    false
---
Language: Cpp
DerivePointerAlignment: false
PointerAlignment: Left
---
Language: JavaScript
ColumnLimit: 128
---
Language: Proto
DisableFormat: true
---
Language: CSharp
ColumnLimit: 128
...

```









## 使用
```bash
# 格式化单个文件
clang-format -style=file -i main.cc

# 格式化项目中所有的.cc和.h文件
find . -name "*.cc" -exec clang-format -i {} \;
find . -name "*.h" -exec clang-format -i {} \;
find . -name "*.h.pp" -exec clang-format -i {} \;
find . -regex '.*\.\(cpp\|hpp\|cu\|c\|h\|cc\)' -exec clang-format -style=file -i {} \;

```


```bash
cmake3 ../mysql-server-8032-format/ -DCMAKE_INSTALL_PREFIX=$HOME/local/mysql_8032_format -DMYSQL_DATADIR=$HOME/local/mysql_8032_format/data -DWITH_BOOST=./boost_1_77_0 -DSYSCONFDIR=$HOME/local/mysql_8032_format/conf -DTMPDIR=$HOME/local/mysql_8032_format/tmp -DMYSQLX_UNIX_ADDR=$HOME/local/mysql_8032_format/tmp -DWITH_ICU=bundled -DWITH_LZ4=bundled -DWITH_LZMA=bundled -DWITH_PROTOBUF=bundled -DWITH_SSL=system -DWITH_ZLIB=bundled -DWITH_ZSTD=bundled -DWITH_BUILD_ID=1 -DENABLED_LOCAL_INFILE=1 -DENABLE_DOWNLOADS=1 -DMYSQL_TCP_PORT=8032 -DCMAKE_BUILD_TYPE=Debug -DWITH_DEBUG=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DENABLED_LOCAL_INFILE=1 -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_0900_ai_ci -DFORCE_COLORED_OUTPUT=ON --trace --trace-expand -DWITH_ICU=/usr/local/ > cmake3.out 2>cmake


cp -r ../boost_1_77_0 ./


make -j8
```

## 配置
```yaml
---
# We'll use defaults from the LLVM style, but with 4 columns indentation.
BasedOnStyle: LLVM
# 缩进宽度
IndentWidth: 8
---
Language: Cpp
# Force pointers to the type for C++.
DerivePointerAlignment: false
PointerAlignment: Left
---
Language: JavaScript
# Use 100 columns for JS.
ColumnLimit: 100
---
Language: Proto
# Don't format .proto files.
DisableFormat: true
---
Language: CSharp
# Use 100 columns for C#.
ColumnLimit: 100
...
```




##### google样式

最终配置文件：`[root@dev /root/mysql-server-8032-google]# cat .clang-format `
```yaml
---
Language:        Cpp
BasedOnStyle:  Google
AccessModifierOffset: -8
AlignConsecutiveAssignments:
  Enabled:         false
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   false
  AlignFunctionPointers: false
  PadOperators:    true
AllowBreakBeforeNoexceptSpecifier: Never
AllowShortCompoundRequirementOnASingleLine: true
BreakAdjacentStringLiterals: true
BreakAfterAttributes: Leave
ColumnLimit:     0
IndentCaseLabels: false
IndentWidth:     8
SkipMacroDefinitionBody: true
SortIncludes:    Never
SortUsingDeclarations: Never
...

```
> 不要去格式化`mysql-server-8032-google/include`

##### 20240324
> AlignConsecutiveAssignments
>
> IndentCaseLabels
>
> ColumnLimit

```yaml
---
Language:        Cpp
BasedOnStyle:  Google
AccessModifierOffset: -8
AlignConsecutiveAssignments:
  Enabled:         true
  AcrossEmptyLines: false
  AcrossComments:  false
  AlignCompound:   false
  AlignFunctionPointers: false
  PadOperators:    true
AllowBreakBeforeNoexceptSpecifier: Never
AllowShortCompoundRequirementOnASingleLine: true
BreakAdjacentStringLiterals: true
BreakAfterAttributes: Leave
ColumnLimit:     128
IndentWidth:     8
SkipMacroDefinitionBody: true
SortIncludes:    Never
SortUsingDeclarations: Never
...

```


