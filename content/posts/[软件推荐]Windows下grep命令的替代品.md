---
title: "[软件推荐]Windows下grep命令的替代品"
categories: [ "软件推荐" ]
tags: [ "windows","grep","实用工具","dngrep" ]
draft: false
slug: "grep-on-windows"
date: "2024-09-11 21:51:00"
---

grep 命令全名 **global regular expression print**（全局正则表达式打印），可以搜索一个或多个（输入）文件，以查找与「给定模式」相匹配的行，并将每个匹配行写入标准输出，这在Linux下是一个非常常用的命令。无论是筛选文件还是命令输出，基本上都是一个grep搞定。

但是Windows下就没有这么好用的东西（至少自带程序里面没有），只能通过装第三方软件实现，以下推荐几个我在使用的替代品：



#### GNU Grep

##### 官网

[Grep Windows 版 (sourceforge.net)](https://gnuwin32.sourceforge.net/packages/grep.htm)

##### 使用方法

到官网[下载](http://downloads.sourceforge.net/gnuwin32/grep-2.5.4-setup.exe)文件后安装，需要配置环境变量引导到Grep安装目录，然后即可在cmd或powershell中使用：

```shell
grep --help
Usage: grep [OPTION]... PATTERN [FILE]...
Search for PATTERN in each FILE or standard input.
PATTERN is, by default, a basic regular expression (BRE).
Example: grep -i 'hello world' menu.h main.c

Regexp selection and interpretation:
  -E, --extended-regexp     PATTERN is an extended regular expression (ERE)
  -F, --fixed-strings       PATTERN is a set of newline-separated fixed strings
  -G, --basic-regexp        PATTERN is a basic regular expression (BRE)
  -P, --perl-regexp         PATTERN is a Perl regular expression
  -e, --regexp=PATTERN      use PATTERN for matching
  -f, --file=FILE           obtain PATTERN from FILE
  -i, --ignore-case         ignore case distinctions
  -w, --word-regexp         force PATTERN to match only whole words
  -x, --line-regexp         force PATTERN to match only whole lines
  -z, --null-data           a data line ends in 0 byte, not newline

Miscellaneous:
  -s, --no-messages         suppress error messages
  -v, --invert-match        select non-matching lines
  -V, --version             print version information and exit
      --help                display this help and exit
      --mmap                use memory-mapped input if possible

Output control:
  -m, --max-count=NUM       stop after NUM matches
  -b, --byte-offset         print the byte offset with output lines
  -n, --line-number         print line number with output lines
      --line-buffered       flush output on every line
  -H, --with-filename       print the filename for each match
  -h, --no-filename         suppress the prefixing filename on output
      --label=LABEL         print LABEL as filename for standard input
  -o, --only-matching       show only the part of a line matching PATTERN
  -q, --quiet, --silent     suppress all normal output
      --binary-files=TYPE   assume that binary files are TYPE;
                            TYPE is `binary', `text', or `without-match'
  -a, --text                equivalent to --binary-files=text
  -I                        equivalent to --binary-files=without-match
  -d, --directories=ACTION  how to handle directories;
                            ACTION is `read', `recurse', or `skip'
  -D, --devices=ACTION      how to handle devices, FIFOs and sockets;
                            ACTION is `read' or `skip'
  -R, -r, --recursive       equivalent to --directories=recurse
      --include=FILE_PATTERN  search only files that match FILE_PATTERN
      --exclude=FILE_PATTERN  skip files and directories matching FILE_PATTERN
      --exclude-from=FILE   skip files matching any file pattern from FILE
      --exclude-dir=PATTERN directories that match PATTERN will be skipped.
  -L, --files-without-match print only names of FILEs containing no match
  -l, --files-with-matches  print only names of FILEs containing matches
  -c, --count               print only a count of matching lines per FILE
  -T, --initial-tab         make tabs line up (if needed)
  -Z, --null                print 0 byte after FILE name

Context control:
  -B, --before-context=NUM  print NUM lines of leading context
  -A, --after-context=NUM   print NUM lines of trailing context
  -C, --context=NUM         print NUM lines of output context
  -NUM                      same as --context=NUM
      --color[=WHEN],
      --colour[=WHEN]       use markers to highlight the matching strings;
                            WHEN is `always', `never', or `auto'
  -U, --binary              do not strip CR characters at EOL (MSDOS)
  -u, --unix-byte-offsets   report offsets as if CRs were not there (MSDOS)

`egrep' means `grep -E'.  `fgrep' means `grep -F'.
Direct invocation as either `egrep' or `fgrep' is deprecated.
With no FILE, or when FILE is -, read standard input.  If less than two FILEs
are given, assume -h.  Exit status is 0 if any line was selected, 1 otherwise;
if any error occurs and -q was not given, the exit status is 2.

Report bugs to: bug-grep@gnu.org
GNU Grep home page: <http://www.gnu.org/software/grep/>
General help using GNU software: <http://www.gnu.org/gethelp/>
```

```shell
netstat -a | grep localhost #通管筛选命令执行结果
  TCP    127.0.0.1:5354         localhost:49718        ESTABLISHED
  TCP    127.0.0.1:5354         localhost:49722        ESTABLISHED
  ...
```

```shell
grep -rin question * #遍历目录查询关键词位置
cmd/nekobox.go:19:      app.Description = "Anonymous question box"
internal/cmd/censor.go:19:      Usage:  "Censor the questions",
internal/cmd/censor.go:37:      // Check all the unprocessed questions.
...
```

##### 优点

使用方法基本和Linux下一致

##### 缺点

1. 需要配置环境变量位置才能使用
2. 最后一次更新已经是2009年了。

#### dnGrep

官网：[dnGrep – 适用于 Windows 的强大搜索功能](https://dngrep.github.io/)

这是一个GUI化的grep，能够实现使用文本、正则表达式、XPath等查询搜索文本文件、Word 和 Excel 文档、PDF 和存档。dnGrep 包括搜索和替换、整个文件预览、文件资源管理器中的右键单击搜索等等。

使用方法：到[Github项目页面](https://github.com/dnGrep/dnGrep/releases/latest)下载对应版本的安装包安装即可，项目自带中文，安装完成后可以到设置里面配置。使用方法也很简单，无非就是选择查找范围，选择搜索方式和搜索条件，搜索结果会高亮展现：

![dngrep][1]

##### 优点

1. 图形化操作，更直观
2. 搜索方式多样化

##### 缺点

能搜文件，但只能搜文件。

~~对原生爱好者不友好。~~



#### Git-scm

其实这玩意就是个mingw64工具集，本来是用于git的，但是我看到他带bash和grep，安装使用也方便，就一直用了。

##### 官网

[Git - Downloads (git-scm.com)](https://git-scm.com/downloads)

##### 使用方法

下载安装对应版本即可，也提供了便携版(PortableGit)，解压即用。安装之后右键菜单会多出一个`git bash`，在指定目录下点开会进入一个bash终端，在这里就直接能执行grep了。

![git-scm][2]

除了grep外，附带的其他命令可以在`usr/bin/`下看到。

##### 优点

最真实的linux终端体验

##### 缺点

~~如果只为了用grep的话有点为了这碗醋包饺子的感觉。~~


  [1]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/09/11/66e1a05b4dd4f.png
  [2]: https://img-tama-guru.oss-cn-hongkong.aliyuncs.com/2024/09/11/66e1a0404ca95.png