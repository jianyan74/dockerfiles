## PHP 基础说明

目录

- 基本命令
- 命令行脚本的开发

### 基本命令

参数 | 长名称 | 说明
---|---|---
-a | --interactive | 交互式运行 PHP。如果编译 PHP 时加入了 Readline 扩展（Windows 下不可用），那将会得到一个很好的外壳，包括一个自动完成的功能（例如可以在键入变量名的时候，按下 TAB 键，PHP 会自动完成该变量名）以及命令历史记录，可以用上下键来访问。历史记录存在 ~/.php_history 文件中。
-c | --php-ini | 用该参数，可以指定一个放置 php.ini 文件的目录，或者直接指定一个自定义的 INI 文件（其文件名可以不是 php.ini），例如：php -c /custom/directory/ my_script.php 或 php -c /custom/directory/custom-file.ini my_script.php。
-n | --no-php-ini | 完全忽略 php.ini。此参数在 PHP 4.3.0 以后有效。
-d | --define | 用该参数可以自行设置任何可以在 php.ini 文件中设置的配置选项的值，其语法为： -d configuration_directive[=value]
-e | --profile-info | 激活扩展信息模式，被用于调试／测试。
-f | --file | 解析并运行 -f 选项给定的文件名。该参数为可选参数，可以省略，仅指明需要运行的文件名即可。
-i | --info | 该命令行参数会调用 phpinfo() 函数并显示出结果。如果 PHP 没有正常工作，建议执行 php -i 命令来查看在信息表格之前或者对应的地方是否有任何错误信息输出。请注意当使用 CGI 摸索时，输出的内容为 HTML 格式，因此输出的信息篇幅较大。
-l | --syntax-check | 该参数提供了对指定 PHP 代码进行语法检查的方便的方法。如果成功，则向标准输出写入 No syntax errors detected in <filename> 字符串，并且外壳返回值为 0。如果失败，则输出 Errors parsing <filename> 以及内部解析器错误信息到标准输出，同时外壳返回值将别设置为 255。 <br />该参数将无法检查致命错误（如未定义函数），如果也希望检测致命错误，请使用 -f 参数。 该参数不能和 -r 一同使用。
-m | --modules | 使用该参数，PHP 将打印出内置以及已加载的 PHP 及 Zend 模块。
-r | --run | 使用该参数可以在命令行内运行单行 PHP 代码。无需加上 PHP 的起始和结束标识符（<?php 和 ?>），否则将会导致语法解析错误。
-B | --process-begin | 在处理 stdin 之前先执行 PHP 代码。PHP 5 新加。
-R | --process-code | 对每个输入行都执行 PHP 代码。PHP 5 新加。 此模式下有两个特殊变量：argn 和argi。argn 包含 PHP 当前处理的行内容，而argi 则包含该行号。
-F | --process-file | 对每个输入行都执行 PHP 文件。PHP 5 新加。
-E | --process-end | 在处理完输入后执行的 PHP 代码。PHP 5 新加。
-s | --syntax-highlight | 显示有语法高亮色彩的源代码。 感觉没啥用。
-v | --version | 将 PHP，PHP SAPI 和 Zend 的版本信息写入标准输出。
-w | --strip | 显示除去了注释和多余空白的源代码。
-z | --zend-extension | 加载 Zend 扩展库。如果仅给定一个文件名，PHP 将试图从当前系统扩展库的默认路径（在 Linux 系统下，该路径通常由 /etc/ld.so.conf 指定）加载该扩展库。如果用一个绝对路径指定文件名，则不会使用系统的扩展库默认路径。如果用相对路径指定的文件名，则 PHP 仅试图在当前目录的相对目录加载扩展库。
--ini | 显示配置文件名称。
--rf | 显示指定方法相关信息。对语言结构无效。echo、print 等语言结构不可以。
--rc | 显示指定类相关信息。
--re |显示指定扩展相关信息。
--rz | 显示指定 Zend 扩展相关信息。
--ri | 显示指定扩展的配置信息。

例如：

显示编译到内核下的模块

```
php -m
```

查看指定配置信息

```
php -i|grep session
```

查看扩展是否存在

```
php --re yafs
```

查看扩展配置信息

```
php --ri yaf
```

### 命令行脚本的开发

> index.php 内容

```
<?php

echo '命令行参数个数: ' . $argc . "n";
echo "命令行参数:n";
foreach ($argv as $index => $arg) {
    echo "{$index} : {$arg}\n";
}
```

1、执行

```
php index.php 1 2 3
```

2、输出的结果

```
命令行参数个数: 4
命令行参数:
   0 : index.php
   1 : 1
   2 : 2
   3 : 3
```
