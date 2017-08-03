title: 用c++为php编写扩展
date: 2016-12-08 10:52:40
tags: "c++"
top: true
sticky: 1
---
<Excerpt in index | 首页摘要> 
用c/c++为php编写动态链接库<!-- more -->
<The rest of contents | 余下全文>
## 准备工作：

1. 下载PHP源码 http://www.php.net/downloads
由于博主的php版本为5.4比较老测试扩展时出现各种问题，故这里编译安装目前最新版 `php-7.1.0.tar.gz`
2. 解压后，源码放到 `/root/source/php-7.1.0`
3. 安装目录：`/usr/local/php7`
4. 开始安装，配置 `/usr/local/php7/etc/php.ini` 的路径，方便以后进行个性化配置
5. 扩展名称：fhy
6. 扩展函数：fhy_say()，这个函数只返回一个"Hello world!" 字符串
7. 扩展可运行在 win32 系统，也运行在类unix系统，但是需要编译不同的文件，这里只介绍　GNU/Linux 下的操作。

## 开始编写扩展：

1. 创建要实现的函数列表文件 `/root/source/fhy.proto` 内容如下：
使用ext_skel脚本加入 `--proto=/root/source/fhy.proto` 参数可直接构建所需函数
```c
string fhy_say()
```

2. 使用扩展骨架工具生成核心文件，命令如下：

```bash
[root@localhost ext]# pwd
/root/source/php-7.1.0/ext
[root@localhost ext]# ./ext_skel --extname=fhy --proto=/root/source/fhy.proto
Creating directory fhy
awk: /root/source/php-7.1.0/ext/skeleton/create_stubs:56: warning: escape sequence `\|' treated as plain `|'
Creating basic files: config.m4 config.w32 .gitignore fhy.c php_fhy.h CREDITS EXPERIMENTAL tests/001.phpt fhy.php [done].

To use your new extension, you will have to execute the following steps:

1.  $ cd ..
2.  $ vi ext/fhy/config.m4
3.  $ ./buildconf
4.  $ ./configure --[with|enable]-fhy
5.  $ make
6.  $ ./sapi/cli/php -f ext/fhy/fhy.php
7.  $ vi ext/fhy/fhy.c
8.  $ make

Repeat steps 3-6 until you are satisfied with ext/fhy/config.m4 and
step 6 confirms that your module is compiled into PHP. Then, start writing
code and repeat the last two steps as often as necessary.
```

这时就在 ext 目录下出现了 fhy 文件夹，里面包含几个文件，如：config.m4 fhy.c  php_fhy.h 等等。


3\. 修改config.m4文件，内容如下：

```bash
dnl $Id$
dnl config.m4 for extension fhy

dnl Comments in this file start with the string 'dnl'.
dnl Remove where necessary. This file will not work
dnl without editing.

dnl If your extension references something external, use with:

dnl PHP_ARG_WITH(fhy, for fhy support,
dnl Make sure that the comment is aligned:
dnl [  --with-fhy             Include fhy support])

dnl Otherwise use enable:

dnl PHP_ARG_ENABLE(fhy, whether to enable fhy support,
dnl Make sure that the comment is aligned:
dnl [  --enable-fhy           Enable fhy support])

if test "$PHP_FHY" != "no"; then
dnl $Id$
dnl config.m4 for extension fhy

dnl Comments in this file start with the string 'dnl'.
dnl Remove where necessary. This file will not work
dnl without editing.

dnl If your extension references something external, use with:

dnl PHP_ARG_WITH(fhy, for fhy support,
dnl Make sure that the comment is aligned:
dnl [  --with-fhy             Include fhy support])

dnl Otherwise use enable:

PHP_ARG_ENABLE(fhy, whether to enable fhy support,
Make sure that the comment is aligned:
[  --enable-fhy           Enable fhy support])

if test "$PHP_FHY" != "no"; then
  PHP_REQUIRE_CXX()    dnl 通知Make使用g++
  PHP_ADD_LIBRARY(stdc++, 1, EXTRA_LDFLAGS)    dnl 加入C++标准库
  PHP_NEW_EXTENSION(fhy, fhy.c, $ext_shared,, -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1)
fi
```

这个文件中 dnl 是注释符，其之后的字串是解释上下文。

修改 `fhy.c` 文件重名为 `fhy.cpp`（用c++嘛所以改成cpp了）

4.1. 加入需要的C++ string 头文件，如下：

```bash
#include "php.h"
#include "php_ini.h"
#include "ext/standard/info.h"
#include "php_fhy.h"
#include <string> /*引入c++标准库*/
```

4.2. 修改 fhy_say 函数，如下：

```bash
/* {{{ proto string fhy_say()
    */
PHP_FUNCTION(fhy_say)
{
    std::string str = "Hello world!";
    RETURN_STRINGL(str.c_str(), str.length());
}
```

5. 编译扩展（动态编译）
```bash
[root@localhost fhy]# pwd
/root/source/php-7.1.0/ext/fhy
[root@localhost fhy]# /usr/local/php7/bin/phpize
Configuring for:
PHP Api Version:         20160303
Zend Module Api No:      20160303
Zend Extension Api No:   320160303
[root@localhost fhy]# ./configure --with-php-config=/usr/local/php7/bin/php-config
checking for grep that handles long lines and -e... /usr/bin/grep
checking for egrep... /usr/bin/grep -E
checking for a sed that does not truncate output... /usr/bin/sed
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking whether cc understands -c and -o together... yes
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-unknown-linux-gnu
checking host system type... x86_64-unknown-linux-gnu
checking target system type... x86_64-unknown-linux-gnu
checking for PHP prefix... /usr/local/php7
checking for PHP includes... -I/usr/local/php7/include/php -I/usr/local/php7/include/php/main -I/usr/local/php7/include/php/TSRM -I/usr/local/php7/include/php/Zend -I/usr/local/php7/include/php/ext -I/usr/local/php7/include/php/ext/date/lib
checking for PHP extension directory... /usr/local/php7/lib/php/extensions/no-debug-non-zts-20160303
checking for PHP installed headers prefix... /usr/local/php7/include/php
checking if debug is enabled... no
checking if zts is enabled... no
checking for re2c... no
configure: WARNING: You will need re2c 0.13.4 or later if you want to regenerate PHP parsers.
checking for gawk... gawk
checking whether to enable fhy support... yes, shared
checking for g++... g++
checking whether we are using the GNU C++ compiler... yes
checking whether g++ accepts -g... yes
checking how to run the C++ preprocessor... g++ -E
checking for ld used by cc... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for /usr/bin/ld option to reload object files... -r
checking for BSD-compatible nm... /usr/bin/nm -B
checking whether ln -s works... yes
checking how to recognize dependent libraries... pass_all
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking dlfcn.h usability... yes
checking dlfcn.h presence... yes
checking for dlfcn.h... yes
checking how to run the C++ preprocessor... g++ -E
checking the maximum length of command line arguments... 1572864
checking command to parse /usr/bin/nm -B output from cc object... ok
checking for objdir... .libs
checking for ar... ar
checking for ranlib... ranlib
checking for strip... strip
checking if cc supports -fno-rtti -fno-exceptions... no
checking for cc option to produce PIC... -fPIC
checking if cc PIC flag -fPIC works... yes
checking if cc static flag -static works... no
checking if cc supports -c -o file.o... yes
checking whether the cc linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... no

creating libtool
appending configuration tag "CXX" to libtool
checking for ld used by g++... /usr/bin/ld -m elf_x86_64
checking if the linker (/usr/bin/ld -m elf_x86_64) is GNU ld... yes
checking whether the g++ linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking for g++ option to produce PIC... -fPIC
checking if g++ PIC flag -fPIC works... yes
checking if g++ static flag -static works... no
checking if g++ supports -c -o file.o... yes
checking whether the g++ linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking dynamic linker characteristics... GNU/Linux ld.so
(cached) (cached) checking how to hardcode library paths into programs... immediate
configure: creating ./config.status
config.status: creating config.h

```
5.1. make

```bash
[root@localhost fhy]# make
/bin/sh /root/source/php-7.1.0/ext/fhy/libtool --mode=compile cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/root/source/php-7.1.0/ext/fhy -DPHP_ATOM_INC -I/root/source/php-7.1.0/ext/fhy/include -I/root/source/php-7.1.0/ext/fhy/main -I/root/source/php-7.1.0/ext/fhy -I/usr/local/php7/include/php -I/usr/local/php7/include/php/main -I/usr/local/php7/include/php/TSRM -I/usr/local/php7/include/php/Zend -I/usr/local/php7/include/php/ext -I/usr/local/php7/include/php/ext/date/lib  -DHAVE_CONFIG_H  -g -O2   -c /root/source/php-7.1.0/ext/fhy/fhy.cpp -o fhy.lo
 cc -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1 -I. -I/root/source/php-7.1.0/ext/fhy -DPHP_ATOM_INC -I/root/source/php-7.1.0/ext/fhy/include -I/root/source/php-7.1.0/ext/fhy/main -I/root/source/php-7.1.0/ext/fhy -I/usr/local/php7/include/php -I/usr/local/php7/include/php/main -I/usr/local/php7/include/php/TSRM -I/usr/local/php7/include/php/Zend -I/usr/local/php7/include/php/ext -I/usr/local/php7/include/php/ext/date/lib -DHAVE_CONFIG_H -g -O2 -c /root/source/php-7.1.0/ext/fhy/fhy.cpp  -fPIC -DPIC -o .libs/fhy.o
/bin/sh /root/source/php-7.1.0/ext/fhy/libtool --mode=link cc -DPHP_ATOM_INC -I/root/source/php-7.1.0/ext/fhy/include -I/root/source/php-7.1.0/ext/fhy/main -I/root/source/php-7.1.0/ext/fhy -I/usr/local/php7/include/php -I/usr/local/php7/include/php/main -I/usr/local/php7/include/php/TSRM -I/usr/local/php7/include/php/Zend -I/usr/local/php7/include/php/ext -I/usr/local/php7/include/php/ext/date/lib  -DHAVE_CONFIG_H  -g -O2   -o fhy.la -export-dynamic -avoid-version -prefer-pic -module -rpath /root/source/php-7.1.0/ext/fhy/modules -lstdc++ fhy.lo
cc -shared  .libs/fhy.o  -lstdc++  -Wl,-soname -Wl,fhy.so -o .libs/fhy.so
creating fhy.la
(cd .libs && rm -f fhy.la && ln -s ../fhy.la fhy.la)
/bin/sh /root/source/php-7.1.0/ext/fhy/libtool --mode=install cp ./fhy.la /root/source/php-7.1.0/ext/fhy/modules
cp ./.libs/fhy.so /root/source/php-7.1.0/ext/fhy/modules/fhy.so
cp ./.libs/fhy.lai /root/source/php-7.1.0/ext/fhy/modules/fhy.la
PATH="$PATH:/sbin" ldconfig -n /root/source/php-7.1.0/ext/fhy/modules
----------------------------------------------------------------------
Libraries have been installed in:
   /root/source/php-7.1.0/ext/fhy/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.
```
生成的扩展文件：

```bash
[root@localhost modules]# pwd
/root/source/php-7.1.0/ext/fhy/modules
[root@localhost modules]# ll
总用量 60
-rw-r--r--. 1 root root   783 Dec  8 11:25 fhy.la
-rwxr-xr-x. 1 root root 54371 Dec  8 11:25 fhy.so
```

将`fhy.so` 放到php扩展目录 `/usr/local/php7/lib/php/extensions/no-debug-non-zts-20160303/`
`php.ini` 引入扩展文件

## 测试扩展

```bash
[root@localhost www]# cat test.php
<?php
echo fhy_say()."\n";
echo confirm_fhy_compiled('fhy')."\n";
[root@localhost www]# php test.php
Hello world!
Congratulations! You have successfully modified ext/fhy/config.m4. Module fhy is now compiled into PHP.

```