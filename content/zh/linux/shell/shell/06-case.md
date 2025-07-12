---
title: 6. case
description: case
date: 2024-09-06
weight: 600
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>






```bash
# Be more Bourne compatible
DUALCASE=1
export DUALCASE # for MKS sh
as_nop=:
if test ${ZSH_VERSION+y} && (emulate sh) >/dev/null 2>&1; then
	:
	emulate sh
	NULLCMD=:
	# Pre-4.2 versions of Zsh do word splitting on ${1+"$@"}, which
	# is contrary to our usage.  Disable this feature.
	alias -g '${1+"$@"}'='"$@"'
	setopt NO_GLOB_SUBST
else
	$as_nop
	case $( (set -o) 2>/dev/null) in
	*posix*)
		:
		set -o posix
		;;
	*)
		:
		;;
	esac
fi

```

- 模糊匹配`*posix*`
- `:` shell中的空操作











