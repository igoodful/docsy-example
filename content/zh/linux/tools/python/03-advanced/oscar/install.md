---
title: 安装
date: 2023-09-30
weight: 200
description: >
  oscar 学习.
---
{{< alert >}}

{{< /alert >}}

## 一、安装

```bash
# 根据python版本选择对应的python3.xx-devel包，否则找不到
yum -y install python3.11-devel

pip3.11 install mysqlclient

python3.11 manage.py migrate

python3.11 manage.py createsuperuser

pip3.11 install pycountry

python3.11 manage.py oscar_populate_countries

python3.11 manage.py runserver 0.0.0.0:8000

mkdir templates static media
```




```bash
mkdir shop

cd shop/

python3.11 -m venv oscar

source oscar/bin/activate

pip3.11 install django-oscar[sorl-thumbnail] -i https://pypi.org/simple

django-admin startproject shop


```

#### 安装详情

```bash
(oscar) C:\hugo\now\oscar\Scripts>pip3.exe install django-oscar[sorl-thumbnail] -i https://pypi.org/simple
Collecting django-oscar[sorl-thumbnail]
  Downloading django_oscar-3.2.4-py3-none-any.whl (9.3 MB)
     ---------------------------------------- 9.3/9.3 MB 1.5 MB/s eta 0:00:00
Collecting django<4.3,>=3.2
  Downloading Django-4.2.11-py3-none-any.whl (8.0 MB)
     ---------------------------------------- 8.0/8.0 MB 1.6 MB/s eta 0:00:00
Collecting pillow>=6.0
  Downloading pillow-10.3.0-cp311-cp311-win_amd64.whl (2.5 MB)
     ---------------------------------------- 2.5/2.5 MB 1.7 MB/s eta 0:00:00
Collecting django-extra-views<0.15,>=0.13
  Downloading django-extra-views-0.14.0.tar.gz (12 kB)
  Preparing metadata (setup.py) ... done
Collecting django-haystack>=3.0b1
  Downloading django-haystack-3.2.1.tar.gz (466 kB)
     ---------------------------------------- 466.6/466.6 kB 1.8 MB/s eta 0:00:00
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Preparing metadata (pyproject.toml) ... done
Collecting django-treebeard>=4.3.0
  Downloading django_treebeard-4.7.1-py3-none-any.whl (93 kB)
     ---------------------------------------- 93.2/93.2 kB 663.8 kB/s eta 0:00:00
Collecting Babel<3.0,>=1.0
  Downloading Babel-2.14.0-py3-none-any.whl (11.0 MB)
     ---------------------------------------- 11.0/11.0 MB 1.8 MB/s eta 0:00:00
Collecting purl>=0.7
  Downloading purl-1.6-py2.py3-none-any.whl (10 kB)
Collecting phonenumbers
  Downloading phonenumbers-8.13.33-py2.py3-none-any.whl (2.6 MB)
     ---------------------------------------- 2.6/2.6 MB 2.0 MB/s eta 0:00:00
Collecting django-phonenumber-field<7.0.0,>=4.0.0
  Downloading django_phonenumber_field-6.4.0-py3-none-any.whl (65 kB)
     ---------------------------------------- 65.9/65.9 kB 1.8 MB/s eta 0:00:00
Collecting factory-boy<3.3,>=3.0
  Downloading factory_boy-3.2.1-py2.py3-none-any.whl (35 kB)
Collecting django-tables2<2.4,>=2.3
  Downloading django_tables2-2.3.4-py2.py3-none-any.whl (91 kB)
     ---------------------------------------- 91.6/91.6 kB 2.6 MB/s eta 0:00:00
Collecting django-widget-tweaks>=1.4.1
  Downloading django_widget_tweaks-1.5.0-py3-none-any.whl (9.0 kB)
Collecting sorl-thumbnail<12.10,>=12.9
  Downloading sorl_thumbnail-12.9.0-py3-none-any.whl (41 kB)
     ---------------------------------------- 41.7/41.7 kB 2.0 MB/s eta 0:00:00
Collecting asgiref<4,>=3.6.0
  Downloading asgiref-3.8.1-py3-none-any.whl (23 kB)
Collecting sqlparse>=0.3.1
  Using cached sqlparse-0.4.4-py3-none-any.whl (41 kB)
Collecting tzdata
  Downloading tzdata-2024.1-py2.py3-none-any.whl (345 kB)
     ---------------------------------------- 345.4/345.4 kB 1.3 MB/s eta 0:00:00
Collecting Faker>=0.7.0
  Downloading Faker-24.4.0-py3-none-any.whl (1.8 MB)
     ---------------------------------------- 1.8/1.8 MB 1.4 MB/s eta 0:00:00
Collecting six
  Using cached six-1.16.0-py2.py3-none-any.whl (11 kB)
Collecting python-dateutil>=2.4
  Downloading python_dateutil-2.9.0.post0-py2.py3-none-any.whl (229 kB)
     ---------------------------------------- 229.9/229.9 kB 1.4 MB/s eta 0:00:00
Building wheels for collected packages: django-haystack
  Building wheel for django-haystack (pyproject.toml) ... done
  Created wheel for django-haystack: filename=django_haystack-3.2.1-py3-none-any.whl size=141866 sha256=fba66e390e98003dfbc39f2f902a80f4e5b99e4e1799be51680938bb5438c829
  Stored in directory: c:\users\wl\appdata\local\pip\cache\wheels\fa\ad\f3\60909d1063b1b3d710f88952e0ff03942307dc7668ac379a5e
Successfully built django-haystack
Installing collected packages: phonenumbers, tzdata, sqlparse, sorl-thumbnail, six, pillow, django-widget-tweaks, Babel, asgiref, python-dateutil, purl, django, Faker, django-treebeard, django-tables2, django-phonenumber-field, django-haystack, django-extra-views, factory-boy, django-oscar
  DEPRECATION: django-extra-views is being installed using the legacy 'setup.py install' method, because it does not have a 'pyproject.toml' and the 'wheel' package is not installed. pip 23.1 will enforce this behaviour change. A possible replacement is to enable the '--use-pep517' option. Discussion can be found at https://github.com/pypa/pip/issues/8559
  Running setup.py install for django-extra-views ... done
Successfully installed Babel-2.14.0 Faker-24.4.0 asgiref-3.8.1 django-4.2.11 django-extra-views-0.14.0 django-haystack-3.2.1 django-oscar-3.2.4 django-phonenumber-field-6.4.0 django-tables2-2.3.4 django-treebeard-4.7.1 django-widget-tweaks-1.5.0 factory-boy-3.2.1 phonenumbers-8.13.33 pillow-10.3.0 purl-1.6 python-dateutil-2.9.0.post0 six-1.16.0 sorl-thumbnail-12.9.0 sqlparse-0.4.4 tzdata-2024.1

[notice] A new release of pip available: 22.3.1 -> 24.0
[notice] To update, run: python.exe -m pip install --upgrade pip

```


#### 官方源
```bash
pip config set global.index-url  https://pypi.org/simple
pip install xx -i https://pypi.org/simple
```

#### 国内源
```bash
# 清华源
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 阿里源
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/

# 腾讯源
pip config set global.index-url http://mirrors.cloud.tencent.com/pypi/simple

# 豆瓣源
pip config set global.index-url http://pypi.douban.com/simple/
```


## 二、配置

| 配置项             | 建议值                                           | 说明                                             |
|:-------------------|:-------------------------------------------------|:-------------------------------------------------|
| ALLOWED_HOSTS      | `['*']`                                          | 允许所有机器访问，默认只能本地访问                |
| INSTALLED_APPS     | `app_name`                                       | 增减项目的新应用app                              |
| LANGUAGE_CODE      | `zh-hans`                                        | 语言配置，默认值为`en-us`                         |
| DATABASES          | `mysql`                                          | 数据库配置，默认为`sqlite3 `                      |
| DEFAULT_AUTO_FIELD | `django.db.models.BigAutoField`                  | 默认值                                           |
| STATIC_URL         | `static/`                                        | 默认值，定义静态文件`URL`，CSS、JavaScript、image    |
| STATIC_ROOT        | `os.path.join(BASE_DIR, 'static')`               | 默认值，`manage.py collectstatic`收集静态文件     |
| STATICFILES_DIRS   |                                                  | 默认值为空，其他文件夹中也有静态文件，列表         |
| MEDIA_URL          | `/media/`                                        | 放置用户上传的文件                               |
| MEDIA_ROOT         | `os.path.join(BASE_DIR, 'media')`                |                                                  |
| MIDDLEWARE         |                                                  | 注册中间件,自己写的中间件，配置要写在系统中的后面 |
| ROOT_URLCONF       | `shop.urls`                                      | 指定主路由                                       |
| TEMPLATES          | `'DIRS': [os.path.join(BASE_DIR, 'templates')],` | 指定用于存储 HTML 模板的目录                     |
| LOGIN_URL          |                                                  |                                                  |
| WSGI_APPLICATION   |                                                  | 正式启动的时候会用                               |
| TIME_ZONE          | `Asia/Shanghai`                                  | 时区设置,默认值世界标准时区`UTC`                 |
| USE_I18N           | `True`                                           | 是否启用自动翻译系统                             |
| USE_L10N           | `True`                                           | 是否以本地化格式显示数字和时间                   |
| USE_TZ             |                                                  | 若使用了本地时间，必须设为False                   |
| BASE_DIR           |                                                  | 一般不要修改，所有文件夹都依赖于此路径            |
| SECRET_KEY         |                                                  | 秘钥                                             |
| DEBUG              |                                                  | 调试。开发开启，部署开关                           |
| CACHES             |                                                  | 配置缓存                                         |


#### STATIC_URL

- `STATIC_URL = '/static/'`：这表示静态文件的 URL 前缀是以斜杠开头的绝对路径。例如，如果你的网站域名是 `example.com`，那么静态文件的 URL 将是 `example.com/static/`。这种设置适用于大多数情况下，特别是当你的静态文件位于网站根目录的 static 文件夹中时。

- `STATIC_URL = 'static/'`：这表示静态文件的 URL 前缀是相对路径，不以斜杠开头。例如，如果你的网站域名是 `example.com`，那么静态文件的 URL 将是 `example.com/yourapp/static/`。这种设置通常用于部署在子路径或子域名上的项目，或者当你的静态文件不是位于网站根目录的 static 文件夹中时。

总的来说，两者在功能上没有本质区别，只是表示静态文件 URL 的方式不同。选择哪种方式取决于你的项目结构和部署需求。


## 新增`app`

#### 创建`app`
```bash
python3.11 manage.py startapp work
```

#### 添加`app`到`INSTALLED_APPS`








#### 数据库同步
```bash
python3.11 manage.py makemigrations work
python3.11 manage.py migrate
```




## 问题

#### Python.h问题

1. 没有安装 Python 开发包，则`yum -y install python3.x-devel`。其中 x 是 Python 版本号
   1. 比如要安装 Python 3.6 的开发库，则应该执行以下命令：`yum -y install python3.6-devel`
   2. 比如要安装 Python 3.11 的开发库，则应该执行以下命令：`yum -y install python3.11-devel`
2. Python 版本不兼容，如果你使用的是 Python 3.x，而某些软件包需要 Python 2.x 的头文件，你需要安装相应版本的 Python 开发包
   1. 例如，在 Centos 上，你可以使用以下命令安装 Python 2.x 开发包：`yum -y install python2.7-devel`
3. 头文件路径设置不正确：如果你已经安装了 Python 开发包，但仍然遇到此问题，可能是因为编译器无法找到头文件。
   1. 在这种情况下，你可以使用以下命令将头文件路径添加到编译器选项中：`CFLAGS=-I/usr/include/pythonX.X`,其中，X.X 是你正在使用的 Python 版本号。你可以将此命令添加到你的编译命令中，例如
4. Python环境变量未设置：如果你使用的是 virtualenv 等 Python 虚拟环境，可能需要将 PYTHONPATH 环境变量设置为正确的路径，以便编译器可以找到所需的头文件。
   1. 你可以使用以下命令将 PYTHONPATH 环境变量设置为当前虚拟环境中的 Python 安装路径：`export PYTHONPATH=/path/to/virtualenv/lib/pythonX.X/site-packages`，其中，/path/to/virtualenv 是你的虚拟环境路径，X.X 是你正在使用的 Python 版本号。

通过以上方法解决了头文件问题后，你应该可以成功编译和安装 Python 包。

> 这就是安装的python3.xx-devel包没有对应造成的，比如python3.6-devel，python2.7-devel

- [Python.h](https://zhuanlan.zhihu.com/p/613210828)


```bash
(oscar) 2024-04-03T13:11:56 [root@dev /root/shop] # pip3.11 install mysqlclient
Collecting mysqlclient
  Using cached mysqlclient-2.2.4.tar.gz (90 kB)
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Installing backend dependencies ... done
  Preparing metadata (pyproject.toml) ... done
Building wheels for collected packages: mysqlclient
  Building wheel for mysqlclient (pyproject.toml) ... error
  error: subprocess-exited-with-error

  × Building wheel for mysqlclient (pyproject.toml) did not run successfully.
  │ exit code: 1
  ╰─> [45 lines of output]
      Trying pkg-config --exists mysqlclient
      # Options for building extension module:
        extra_compile_args: ['-I/usr/include/mysql', '-m64', '-std=c99']
        extra_link_args: ['-L/usr/lib64/mysql', '-lmysqlclient']
        define_macros: [('version_info', (2, 2, 4, 'final', 0)), ('__version__', '2.2.4')]
      running bdist_wheel
      running build
      running build_py
      creating build
      creating build/lib.linux-x86_64-cpython-311
      creating build/lib.linux-x86_64-cpython-311/MySQLdb
      copying src/MySQLdb/__init__.py -> build/lib.linux-x86_64-cpython-311/MySQLdb
      copying src/MySQLdb/_exceptions.py -> build/lib.linux-x86_64-cpython-311/MySQLdb
      copying src/MySQLdb/connections.py -> build/lib.linux-x86_64-cpython-311/MySQLdb
      copying src/MySQLdb/converters.py -> build/lib.linux-x86_64-cpython-311/MySQLdb
      copying src/MySQLdb/cursors.py -> build/lib.linux-x86_64-cpython-311/MySQLdb
      copying src/MySQLdb/release.py -> build/lib.linux-x86_64-cpython-311/MySQLdb
      copying src/MySQLdb/times.py -> build/lib.linux-x86_64-cpython-311/MySQLdb
      creating build/lib.linux-x86_64-cpython-311/MySQLdb/constants
      copying src/MySQLdb/constants/CLIENT.py -> build/lib.linux-x86_64-cpython-311/MySQLdb/constants
      copying src/MySQLdb/constants/CR.py -> build/lib.linux-x86_64-cpython-311/MySQLdb/constants
      copying src/MySQLdb/constants/ER.py -> build/lib.linux-x86_64-cpython-311/MySQLdb/constants
      copying src/MySQLdb/constants/FIELD_TYPE.py -> build/lib.linux-x86_64-cpython-311/MySQLdb/constants
      copying src/MySQLdb/constants/FLAG.py -> build/lib.linux-x86_64-cpython-311/MySQLdb/constants
      copying src/MySQLdb/constants/__init__.py -> build/lib.linux-x86_64-cpython-311/MySQLdb/constants
      running egg_info
      writing src/mysqlclient.egg-info/PKG-INFO
      writing dependency_links to src/mysqlclient.egg-info/dependency_links.txt
      writing top-level names to src/mysqlclient.egg-info/top_level.txt
      reading manifest file 'src/mysqlclient.egg-info/SOURCES.txt'
      reading manifest template 'MANIFEST.in'
      adding license file 'LICENSE'
      writing manifest file 'src/mysqlclient.egg-info/SOURCES.txt'
      copying src/MySQLdb/_mysql.c -> build/lib.linux-x86_64-cpython-311/MySQLdb
      running build_ext
      building 'MySQLdb._mysql' extension
      creating build/temp.linux-x86_64-cpython-311
      creating build/temp.linux-x86_64-cpython-311/src
      creating build/temp.linux-x86_64-cpython-311/src/MySQLdb
      gcc -pthread -Wsign-compare -DDYNAMIC_ANNOTATIONS_ENABLED=1 -DNDEBUG -O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -m64 -mtune=generic -fasynchronous-unwind-tables -fstack-clash-protection -fcf-protection -D_GNU_SOURCE -fPIC -fwrapv -O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -m64 -mtune=generic -fasynchronous-unwind-tables -fstack-clash-protection -fcf-protection -D_GNU_SOURCE -fPIC -fwrapv -O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -m64 -mtune=generic -fasynchronous-unwind-tables -fstack-clash-protection -fcf-protection -D_GNU_SOURCE -fPIC -fwrapv -fPIC "-Dversion_info=(2, 2, 4, 'final', 0)" -D__version__=2.2.4 -I/root/shop/oscar/include -I/usr/include/python3.11 -c src/MySQLdb/_mysql.c -o build/temp.linux-x86_64-cpython-311/src/MySQLdb/_mysql.o -I/usr/include/mysql -m64 -std=c99
      src/MySQLdb/_mysql.c:47:10: fatal error: Python.h: No such file or directory
         47 | #include "Python.h"
            |          ^~~~~~~~~~
      compilation terminated.
      error: command '/opt/rh/gcc-toolset-12/root/usr/bin/gcc' failed with exit code 1
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
  ERROR: Failed building wheel for mysqlclient
Failed to build mysqlclient
ERROR: Could not build wheels for mysqlclient, which is required to install pyproject.toml-based projects

[notice] A new release of pip available: 22.3.1 -> 24.0
[notice] To update, run: pip install --upgrade pip

```





