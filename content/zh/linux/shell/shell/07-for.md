---
title: 7. for
description: for
date: 2024-09-06
weight: 700
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



## for xxx;do  done

`for ac_option` 等价于 `for ac_option in "$@"`


for没有in则它会默认使用位置参数（即$@）。所以这个循环会遍历所有传递给脚本或函数的参数。

```bash
for ac_option; do
	# If the previous option needs an argument, assign it.
	if test -n "$ac_prev"; then
		eval $ac_prev=\$ac_option
		ac_prev=
		continue
	fi

	case $ac_option in
	*=?*) ac_optarg=$(expr "X$ac_option" : '[^=]*=\(.*\)') ;;
	*=) ac_optarg= ;;
	*) ac_optarg=yes ;;
	esac

	case $ac_dashdash$ac_option in
	--)
		ac_dashdash=yes
		;;

	-bindir | --bindir | --bindi | --bind | --bin | --bi)
		ac_prev=bindir
		;;
	-bindir=* | --bindir=* | --bindi=* | --bind=* | --bin=* | --bi=*)
		bindir=$ac_optarg
		;;

	-build | --build | --buil | --bui | --bu)
		ac_prev=build_alias
		;;
	-build=* | --build=* | --buil=* | --bui=* | --bu=*)
		build_alias=$ac_optarg
		;;

	-cache-file | --cache-file | --cache-fil | --cache-fi | \
		--cache-f | --cache- | --cache | --cach | --cac | --ca | --c)
		ac_prev=cache_file
		;;
	-cache-file=* | --cache-file=* | --cache-fil=* | --cache-fi=* | \
		--cache-f=* | --cache-=* | --cache=* | --cach=* | --cac=* | --ca=* | --c=*)
		cache_file=$ac_optarg
		;;

	--config-cache | -C)
		cache_file=config.cache
		;;

	-datadir | --datadir | --datadi | --datad)
		ac_prev=datadir
		;;
	-datadir=* | --datadir=* | --datadi=* | --datad=*)
		datadir=$ac_optarg
		;;

	-datarootdir | --datarootdir | --datarootdi | --datarootd | --dataroot | \
		--dataroo | --dataro | --datar)
		ac_prev=datarootdir
		;;
	-datarootdir=* | --datarootdir=* | --datarootdi=* | --datarootd=* | \
		--dataroot=* | --dataroo=* | --dataro=* | --datar=*)
		datarootdir=$ac_optarg
		;;

	-disable-* | --disable-*)
		ac_useropt=$(expr "x$ac_option" : 'x-*disable-\(.*\)')
		# Reject names that are not valid shell variable names.
		expr "x$ac_useropt" : ".*[^-+._$as_cr_alnum]" >/dev/null &&
			as_fn_error $? "invalid feature name: \`$ac_useropt'"
		ac_useropt_orig=$ac_useropt
		ac_useropt=$(printf "%s\n" "$ac_useropt" | sed 's/[-+.]/_/g')
		case $ac_user_opts in
		*"
"enable_$ac_useropt"
"*) ;;
		*)
			ac_unrecognized_opts="$ac_unrecognized_opts$ac_unrecognized_sep--disable-$ac_useropt_orig"
			ac_unrecognized_sep=', '
			;;
		esac
		eval enable_$ac_useropt=no
		;;

	-docdir | --docdir | --docdi | --doc | --do)
		ac_prev=docdir
		;;
	-docdir=* | --docdir=* | --docdi=* | --doc=* | --do=*)
		docdir=$ac_optarg
		;;

	-dvidir | --dvidir | --dvidi | --dvid | --dvi | --dv)
		ac_prev=dvidir
		;;
	-dvidir=* | --dvidir=* | --dvidi=* | --dvid=* | --dvi=* | --dv=*)
		dvidir=$ac_optarg
		;;

	-enable-* | --enable-*)
		ac_useropt=$(expr "x$ac_option" : 'x-*enable-\([^=]*\)')
		# Reject names that are not valid shell variable names.
		expr "x$ac_useropt" : ".*[^-+._$as_cr_alnum]" >/dev/null &&
			as_fn_error $? "invalid feature name: \`$ac_useropt'"
		ac_useropt_orig=$ac_useropt
		ac_useropt=$(printf "%s\n" "$ac_useropt" | sed 's/[-+.]/_/g')
		case $ac_user_opts in
		*"
"enable_$ac_useropt"
"*) ;;
		*)
			ac_unrecognized_opts="$ac_unrecognized_opts$ac_unrecognized_sep--enable-$ac_useropt_orig"
			ac_unrecognized_sep=', '
			;;
		esac
		eval enable_$ac_useropt=\$ac_optarg
		;;

	-exec-prefix | --exec_prefix | --exec-prefix | --exec-prefi | \
		--exec-pref | --exec-pre | --exec-pr | --exec-p | --exec- | \
		--exec | --exe | --ex)
		ac_prev=exec_prefix
		;;
	-exec-prefix=* | --exec_prefix=* | --exec-prefix=* | --exec-prefi=* | \
		--exec-pref=* | --exec-pre=* | --exec-pr=* | --exec-p=* | --exec-=* | \
		--exec=* | --exe=* | --ex=*)
		exec_prefix=$ac_optarg
		;;

	-gas | --gas | --ga | --g)
		# Obsolete; use --with-gas.
		with_gas=yes
		;;

	-help | --help | --hel | --he | -h)
		ac_init_help=long
		;;
	-help=r* | --help=r* | --hel=r* | --he=r* | -hr*)
		ac_init_help=recursive
		;;
	-help=s* | --help=s* | --hel=s* | --he=s* | -hs*)
		ac_init_help=short
		;;

	-host | --host | --hos | --ho)
		ac_prev=host_alias
		;;
	-host=* | --host=* | --hos=* | --ho=*)
		host_alias=$ac_optarg
		;;

	-htmldir | --htmldir | --htmldi | --htmld | --html | --htm | --ht)
		ac_prev=htmldir
		;;
	-htmldir=* | --htmldir=* | --htmldi=* | --htmld=* | --html=* | --htm=* | \
		--ht=*)
		htmldir=$ac_optarg
		;;

	-includedir | --includedir | --includedi | --included | --include | \
		--includ | --inclu | --incl | --inc)
		ac_prev=includedir
		;;
	-includedir=* | --includedir=* | --includedi=* | --included=* | --include=* | \
		--includ=* | --inclu=* | --incl=* | --inc=*)
		includedir=$ac_optarg
		;;

	-infodir | --infodir | --infodi | --infod | --info | --inf)
		ac_prev=infodir
		;;
	-infodir=* | --infodir=* | --infodi=* | --infod=* | --info=* | --inf=*)
		infodir=$ac_optarg
		;;

	-libdir | --libdir | --libdi | --libd)
		ac_prev=libdir
		;;
	-libdir=* | --libdir=* | --libdi=* | --libd=*)
		libdir=$ac_optarg
		;;

	-libexecdir | --libexecdir | --libexecdi | --libexecd | --libexec | \
		--libexe | --libex | --libe)
		ac_prev=libexecdir
		;;
	-libexecdir=* | --libexecdir=* | --libexecdi=* | --libexecd=* | --libexec=* | \
		--libexe=* | --libex=* | --libe=*)
		libexecdir=$ac_optarg
		;;

	-localedir | --localedir | --localedi | --localed | --locale)
		ac_prev=localedir
		;;
	-localedir=* | --localedir=* | --localedi=* | --localed=* | --locale=*)
		localedir=$ac_optarg
		;;

	-localstatedir | --localstatedir | --localstatedi | --localstated | \
		--localstate | --localstat | --localsta | --localst | --locals)
		ac_prev=localstatedir
		;;
	-localstatedir=* | --localstatedir=* | --localstatedi=* | --localstated=* | \
		--localstate=* | --localstat=* | --localsta=* | --localst=* | --locals=*)
		localstatedir=$ac_optarg
		;;

	-mandir | --mandir | --mandi | --mand | --man | --ma | --m)
		ac_prev=mandir
		;;
	-mandir=* | --mandir=* | --mandi=* | --mand=* | --man=* | --ma=* | --m=*)
		mandir=$ac_optarg
		;;

	-nfp | --nfp | --nf)
		# Obsolete; use --without-fp.
		with_fp=no
		;;

	-no-create | --no-create | --no-creat | --no-crea | --no-cre | \
		--no-cr | --no-c | -n)
		no_create=yes
		;;

	-no-recursion | --no-recursion | --no-recursio | --no-recursi | \
		--no-recurs | --no-recur | --no-recu | --no-rec | --no-re | --no-r)
		no_recursion=yes
		;;

	-oldincludedir | --oldincludedir | --oldincludedi | --oldincluded | \
		--oldinclude | --oldinclud | --oldinclu | --oldincl | --oldinc | \
		--oldin | --oldi | --old | --ol | --o)
		ac_prev=oldincludedir
		;;
	-oldincludedir=* | --oldincludedir=* | --oldincludedi=* | --oldincluded=* | \
		--oldinclude=* | --oldinclud=* | --oldinclu=* | --oldincl=* | --oldinc=* | \
		--oldin=* | --oldi=* | --old=* | --ol=* | --o=*)
		oldincludedir=$ac_optarg
		;;

	-prefix | --prefix | --prefi | --pref | --pre | --pr | --p)
		ac_prev=prefix
		;;
	-prefix=* | --prefix=* | --prefi=* | --pref=* | --pre=* | --pr=* | --p=*)
		prefix=$ac_optarg
		;;

	-program-prefix | --program-prefix | --program-prefi | --program-pref | \
		--program-pre | --program-pr | --program-p)
		ac_prev=program_prefix
		;;
	-program-prefix=* | --program-prefix=* | --program-prefi=* | \
		--program-pref=* | --program-pre=* | --program-pr=* | --program-p=*)
		program_prefix=$ac_optarg
		;;

	-program-suffix | --program-suffix | --program-suffi | --program-suff | \
		--program-suf | --program-su | --program-s)
		ac_prev=program_suffix
		;;
	-program-suffix=* | --program-suffix=* | --program-suffi=* | \
		--program-suff=* | --program-suf=* | --program-su=* | --program-s=*)
		program_suffix=$ac_optarg
		;;

	-program-transform-name | --program-transform-name | \
		--program-transform-nam | --program-transform-na | \
		--program-transform-n | --program-transform- | \
		--program-transform | --program-transfor | \
		--program-transfo | --program-transf | \
		--program-trans | --program-tran | \
		--progr-tra | --program-tr | --program-t)
		ac_prev=program_transform_name
		;;
	-program-transform-name=* | --program-transform-name=* | \
		--program-transform-nam=* | --program-transform-na=* | \
		--program-transform-n=* | --program-transform-=* | \
		--program-transform=* | --program-transfor=* | \
		--program-transfo=* | --program-transf=* | \
		--program-trans=* | --program-tran=* | \
		--progr-tra=* | --program-tr=* | --program-t=*)
		program_transform_name=$ac_optarg
		;;

	-pdfdir | --pdfdir | --pdfdi | --pdfd | --pdf | --pd)
		ac_prev=pdfdir
		;;
	-pdfdir=* | --pdfdir=* | --pdfdi=* | --pdfd=* | --pdf=* | --pd=*)
		pdfdir=$ac_optarg
		;;

	-psdir | --psdir | --psdi | --psd | --ps)
		ac_prev=psdir
		;;
	-psdir=* | --psdir=* | --psdi=* | --psd=* | --ps=*)
		psdir=$ac_optarg
		;;

	-q | -quiet | --quiet | --quie | --qui | --qu | --q | \
		-silent | --silent | --silen | --sile | --sil)
		silent=yes
		;;

	-runstatedir | --runstatedir | --runstatedi | --runstated | \
		--runstate | --runstat | --runsta | --runst | --runs | \
		--run | --ru | --r)
		ac_prev=runstatedir
		;;
	-runstatedir=* | --runstatedir=* | --runstatedi=* | --runstated=* | \
		--runstate=* | --runstat=* | --runsta=* | --runst=* | --runs=* | \
		--run=* | --ru=* | --r=*)
		runstatedir=$ac_optarg
		;;

	-sbindir | --sbindir | --sbindi | --sbind | --sbin | --sbi | --sb)
		ac_prev=sbindir
		;;
	-sbindir=* | --sbindir=* | --sbindi=* | --sbind=* | --sbin=* | \
		--sbi=* | --sb=*)
		sbindir=$ac_optarg
		;;

	-sharedstatedir | --sharedstatedir | --sharedstatedi | \
		--sharedstated | --sharedstate | --sharedstat | --sharedsta | \
		--sharedst | --shareds | --shared | --share | --shar | \
		--sha | --sh)
		ac_prev=sharedstatedir
		;;
	-sharedstatedir=* | --sharedstatedir=* | --sharedstatedi=* | \
		--sharedstated=* | --sharedstate=* | --sharedstat=* | --sharedsta=* | \
		--sharedst=* | --shareds=* | --shared=* | --share=* | --shar=* | \
		--sha=* | --sh=*)
		sharedstatedir=$ac_optarg
		;;

	-site | --site | --sit)
		ac_prev=site
		;;
	-site=* | --site=* | --sit=*)
		site=$ac_optarg
		;;

	-srcdir | --srcdir | --srcdi | --srcd | --src | --sr)
		ac_prev=srcdir
		;;
	-srcdir=* | --srcdir=* | --srcdi=* | --srcd=* | --src=* | --sr=*)
		srcdir=$ac_optarg
		;;

	-sysconfdir | --sysconfdir | --sysconfdi | --sysconfd | --sysconf | \
		--syscon | --sysco | --sysc | --sys | --sy)
		ac_prev=sysconfdir
		;;
	-sysconfdir=* | --sysconfdir=* | --sysconfdi=* | --sysconfd=* | --sysconf=* | \
		--syscon=* | --sysco=* | --sysc=* | --sys=* | --sy=*)
		sysconfdir=$ac_optarg
		;;

	-target | --target | --targe | --targ | --tar | --ta | --t)
		ac_prev=target_alias
		;;
	-target=* | --target=* | --targe=* | --targ=* | --tar=* | --ta=* | --t=*)
		target_alias=$ac_optarg
		;;

	-v | -verbose | --verbose | --verbos | --verbo | --verb)
		verbose=yes
		;;

	-version | --version | --versio | --versi | --vers | -V)
		ac_init_version=:
		;;

	-with-* | --with-*)
		ac_useropt=$(expr "x$ac_option" : 'x-*with-\([^=]*\)')
		# Reject names that are not valid shell variable names.
		expr "x$ac_useropt" : ".*[^-+._$as_cr_alnum]" >/dev/null &&
			as_fn_error $? "invalid package name: \`$ac_useropt'"
		ac_useropt_orig=$ac_useropt
		ac_useropt=$(printf "%s\n" "$ac_useropt" | sed 's/[-+.]/_/g')
		case $ac_user_opts in
		*"
"with_$ac_useropt"
"*) ;;
		*)
			ac_unrecognized_opts="$ac_unrecognized_opts$ac_unrecognized_sep--with-$ac_useropt_orig"
			ac_unrecognized_sep=', '
			;;
		esac
		eval with_$ac_useropt=\$ac_optarg
		;;

	-without-* | --without-*)
		ac_useropt=$(expr "x$ac_option" : 'x-*without-\(.*\)')
		# Reject names that are not valid shell variable names.
		expr "x$ac_useropt" : ".*[^-+._$as_cr_alnum]" >/dev/null &&
			as_fn_error $? "invalid package name: \`$ac_useropt'"
		ac_useropt_orig=$ac_useropt
		ac_useropt=$(printf "%s\n" "$ac_useropt" | sed 's/[-+.]/_/g')
		case $ac_user_opts in
		*"
"with_$ac_useropt"
"*) ;;
		*)
			ac_unrecognized_opts="$ac_unrecognized_opts$ac_unrecognized_sep--without-$ac_useropt_orig"
			ac_unrecognized_sep=', '
			;;
		esac
		eval with_$ac_useropt=no
		;;

	--x)
		# Obsolete; use --with-x.
		with_x=yes
		;;

	-x-includes | --x-includes | --x-include | --x-includ | --x-inclu | \
		--x-incl | --x-inc | --x-in | --x-i)
		ac_prev=x_includes
		;;
	-x-includes=* | --x-includes=* | --x-include=* | --x-includ=* | --x-inclu=* | \
		--x-incl=* | --x-inc=* | --x-in=* | --x-i=*)
		x_includes=$ac_optarg
		;;

	-x-libraries | --x-libraries | --x-librarie | --x-librari | \
		--x-librar | --x-libra | --x-libr | --x-lib | --x-li | --x-l)
		ac_prev=x_libraries
		;;
	-x-libraries=* | --x-libraries=* | --x-librarie=* | --x-librari=* | \
		--x-librar=* | --x-libra=* | --x-libr=* | --x-lib=* | --x-li=* | --x-l=*)
		x_libraries=$ac_optarg
		;;

	-*)
		as_fn_error $? "unrecognized option: \`$ac_option'
Try \`$0 --help' for more information"
		;;

	*=*)
		ac_envvar=$(expr "x$ac_option" : 'x\([^=]*\)=')
		# Reject names that are not valid shell variable names.
		case $ac_envvar in #(
		'' | [0-9]* | *[!_$as_cr_alnum]*)
			as_fn_error $? "invalid variable name: \`$ac_envvar'"
			;;
		esac
		eval $ac_envvar=\$ac_optarg
		export $ac_envvar
		;;

	*)
		# FIXME: should be removed in autoconf 3.0.
		printf "%s\n" "$as_me: WARNING: you should use --build, --host, --target" >&2
		expr "x$ac_option" : ".*[^-._$as_cr_alnum]" >/dev/null &&
			printf "%s\n" "$as_me: WARNING: invalid host type: $ac_option" >&2
		: "${build_alias=$ac_option} ${host_alias=$ac_option} ${target_alias=$ac_option}"
		;;

	esac
done

```

