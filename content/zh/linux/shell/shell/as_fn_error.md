---
title: as_fn_error
description: as_fn_error
date: 2023-11-01
weight: 30000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}


{{</alert>}}



### 1. 定义`as_fn_error`


```bash

# as_echo
as_echo='\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\'
as_echo=$as_echo$as_echo$as_echo$as_echo$as_echo
as_echo=$as_echo$as_echo$as_echo$as_echo$as_echo$as_echo
# Prefer a ksh shell builtin over an external printf program on Solaris,
# but without wasting forks for bash or zsh.
if test -z "$BASH_VERSION$ZSH_VERSION" &&
	(test "X$(print -r -- $as_echo)" = "X$as_echo") 2>/dev/null; then
	as_echo='print -r --'
	as_echo_n='print -rn --'
elif (test "X$(printf %s $as_echo)" = "X$as_echo") 2>/dev/null; then
	as_echo='printf %s\n'
	as_echo_n='printf %s'
else
	if test "X$( (/usr/ucb/echo -n -n $as_echo) 2>/dev/null)" = "X-n $as_echo"; then
		as_echo_body='eval /usr/ucb/echo -n "$1$as_nl"'
		as_echo_n='/usr/ucb/echo -n'
	else
		as_echo_body='eval expr "X$1" : "X\\(.*\\)"'
		as_echo_n_body='eval
      arg=$1;
      case $arg in #(
      *"$as_nl"*)
	expr "X$arg" : "X\\(.*\\)$as_nl";
	arg=`expr "X$arg" : ".*$as_nl\\(.*\\)"`;;
      esac;
      expr "X$arg" : "X\\(.*\\)" | tr -d "$as_nl"
    '
		export as_echo_n_body
		as_echo_n='sh -c $as_echo_n_body as_echo'
	fi
	export as_echo_body
	as_echo='sh -c $as_echo_body as_echo'
fi

# as_expr
if expr a : '\(a\)' >/dev/null 2>&1 &&
	test "X$(expr 00001 : '.*\(...\)')" = X001; then
	as_expr=expr
else
	as_expr=false
fi

# as_me: 获取脚本文件名称
as_me=$($as_basename -- "$0" ||
	$as_expr X/"$0" : '.*/\([^/][^/]*\)/*$' \| \
		X"$0" : 'X\(//\)$' \| \
		X"$0" : 'X\(/\)' \| . 2>/dev/null ||
	$as_echo X/"$0" |
	sed '/^.*\/\([^/][^/]*\)\/*$/{
	    s//\1/
	    q
	  }
	  /^X\/\(\/\/\)$/{
	    s//\1/
	    q
	  }
	  /^X\/\(\/\).*/{
	    s//\1/
	    q
	  }
	  s/.*/./; q')


# as_fn_set_status STATUS
# -----------------------
# Set $? to STATUS, without forking.
as_fn_set_status() {
	return $1
} # as_fn_set_status

# as_fn_exit STATUS
# -----------------
# Exit the shell with STATUS, even in a "trap 0" or "set -e" context.
as_fn_exit() {
	set +e
	as_fn_set_status $1
	exit $1
} # as_fn_exit

# as_fn_error STATUS ERROR [LINENO LOG_FD]
# ----------------------------------------
# Output "`basename $0`: error: ERROR" to stderr. If LINENO and LOG_FD are
# provided, also output the error to LOG_FD, referencing LINENO. Then exit the
# script with STATUS, using 1 if that was 0.
as_fn_error() {
	as_status=$1
	test $as_status -eq 0 && as_status=1
	if test "$4"; then
		as_lineno=${as_lineno-"$3"} as_lineno_stack=as_lineno_stack=$as_lineno_stack
		$as_echo "$as_me:${as_lineno-$LINENO}: error: $2" >&$4
	fi
	$as_echo "$as_me: error: $2" >&2
	as_fn_exit $as_status
} # as_fn_error

```
- 报错关键字：`: error: `

- `as_echo='printf %s\n'`
- `as_echo_n='printf %s'`
- `as_expr=expr`


## 2. 使用`as_fn_error`

- 只需给两个参数即可：状态码和错误信息

```bash
as_fn_error $? "cannot find sources ($ac_unique_file) in $srcdir"


```




```viz-dot
digraph "configure readline" {
node [
fontsize = "12"
];
rankdir="LR";

}
```


