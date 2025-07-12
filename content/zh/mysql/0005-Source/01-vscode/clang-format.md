---
title: clang-format
description: clang-format
date: 2023-10-30
weight: 10001


---

{{< alert >}}

- [clang-format](https://www.actionsky.com/2796.html)
- [clang-format](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)

{{< /alert >}}

```bash
find . -regex '.*\.\(cpp\|hpp\|cu\|c\|h\|cc\)' -exec clang-format -style=file -i {} \;
```


## .clang-format

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




