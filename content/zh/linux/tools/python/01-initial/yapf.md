---
title: yapf
description: yapf
date: 2023-10-26
weight: 20000
---


- [yapf](https://github.com/google/yapf)



## 安裝：yapf

`https://pypi.tuna.tsinghua.edu.cn`

```bash
pip install yapf

```

## 命令行格式化

```bash
yapf -i --style '{based_on_style: pep8, indent_width: 8, continuation_indent_width: 8, column_limit: 128}' dbscale_daemon.py
```

```bash
2024-05-10T12:14:18 [root@node70 /root/test_ansible/roles/chaosblade/files] $ yapf --help
usage: yapf [-h] [-v] [-d | -i | -q] [-r | -l START-END] [-e PATTERN]
            [--style STYLE] [--style-help] [--no-local-style] [-p] [-vv]
            [files [files ...]]

Formatter for Python code.

positional arguments:
  files                 reads from stdin when no files are specified.

optional arguments:
  -h, --help            show this help message and exit
  -v, --version         show program's version number and exit
  -d, --diff            print the diff for the fixed source
  -i, --in-place        make changes to files in place
  -q, --quiet           output nothing and set return value
  -r, --recursive       run recursively over directories
  -l START-END, --lines START-END
                        range of lines to reformat, one-based
  -e PATTERN, --exclude PATTERN
                        patterns for files to exclude from formatting
  --style STYLE         specify formatting style: either a style name (for
                        example "pep8" or "google"), or the name of a file
                        with style settings. The default is pep8 unless a
                        .style.yapf or setup.cfg or pyproject.toml file
                        located in the same directory as the source or one of
                        its parent directories (for stdin, the current
                        directory is used).
  --style-help          show style settings and exit; this output can be saved
                        to .style.yapf to make your settings permanent
  --no-local-style      don't search for local style definition
  -p, --parallel        run yapf in parallel when formatting multiple files.
                        Requires concurrent.futures in Python 2.X
  -vv, --verbose        print out file names while processing

```


#### based_on_style
该based_on_style设置确定此自定义样式基于哪种预定义样式（将其视为子类化）。预定义了四种样式：

- pep8（默认）
- google（基于Google Python 风格指南）
- yapf（用于 Google 开源项目）
- facebook



















## 配置：settings.json
```json
{
    "files.autoSave": "afterDelay",
    "python.linting.flake8Enabled": true,
    "editor.formatOnSave": true,
    "python.formatting.provider": "yapf",
    "python.formatting.yapfArgs": [
      "--style={based_on_style=pep8,  indent_width: 8,arithmetic_precedence_indication=True, column_limit=128, coalesce_brackets=True, dedent_closing_brackets=True}"
    ],
    "python.linting.pylintEnabled": false,
    "python.linting.pylintUseMinimalCheckers": false,
    "editor.codeActionsOnSave": null,
    "python.linting.flake8Args": [
        "--max-line-length=248"
    ]
}

```


## 配置：launch.json

```json

```


## 插件

- IntelliCode



