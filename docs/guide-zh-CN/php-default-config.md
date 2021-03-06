## PHP 完整配置说明

```
[PHP] 
; PHP还是一个不断发展的工具，其功能还在不断地删减 
; 而php.ini的设置更改可以反映出相当的变化， 
; 在使用新的PHP版本前，研究一下php.ini会有好处的

;;;;;;;;;;;;;;;;;;; 
; 关于这个文件 ; 
;;;;;;;;;;;;;;;;;;;

; 这个文件控制了PHP许多方面的观点。为了让PHP读取这个文件，它必须被命名为 
; 'php.ini'。PHP 将在这些地方依次查找该文件：当前工作目录；环境变量PHPRC 
; 指明的路径；编译时指定的路径。 
; 在windows下，编译时的路径是Windows安装目录。 
; 在命令行模式下，php.ini的查找路径可以用 -c 参数替代。

; 该文件的语法非常简单。空白字符和用分号';'开始的行被简单地忽略（就象你可能 
; 猜到的一样）。 章节标题（例如 : [Foo]）也被简单地忽略，即使将来它们可能 
; 有某种的意义。 
; 
; 指示被指定使用如下语法： 
; 指示标识符 = 值 
; directive = value 
; 指示标识符 是 *大小写敏感的* - foo=bar 不同于 FOO = bar。 
; 
; 值可以是一个字符串，一个数字，一个 PHP 常量 (如： E_ALL or M_PI), INI 常量中的 
; 一个 (On, Off, True, False, Yes, No and None) ，或是一个表达式 
; (如: E_ALL & ~E_NOTICE), 或是用引号括起来的字符串(" foo" ). 
; 
; INI 文件的表达式被限制于位运算符和括号。 
; | bitwise OR 
; & bitwise AND 
; ~ bitwise NOT 
; ! boolean NOT 
; 
; 布尔标志可用 1, On, True or Yes 这些值置于开的状态。 
; 它们可用 0, Off, False or No 这些值置于关的状态。 
; 
; 一个空字符串可以用在等号后不写任何东西表示，或者用 None 关键字: 
; 
; foo = ; 将foo置为空字符串 
; foo = none ; 将foo置为空字符串 
; foo = " none" ; 将foo置为字符串'none' 
; 
; 如果你值设置中使用常量，而这些常量属于动态调入的扩展库（不是 PHP 的扩展，就是 
; Zend 的扩展），你仅可以调入这些扩展的行*之后*使用这些常量。 
; 
; 所有在 php.ini-dist 文件里设定的值与内建的默认值相同（这是说，如果 php.ini 
; 没被使用或者你删掉了这些行，默认值与之相同）。


;;;;;;;;;;;;;;;;;;;; 
; 语言选项 ; 
;;;;;;;;;;;;;;;;;;;;

engine = On 
; 使 PHP scripting language engine（PHP 脚本语言引擎）在 Apache下有效。 
short_open_tag = On 
; 允许 tags 将被识别。 
asp_tags = Off 
; 允许ASP-style tags 
precision = 14 
; 浮点类型数显示时的有效位数

y2k_compliance = Off 
; 是否打开 2000年适应 (可能在非Y2K适应的浏览器中导致问题)

output_buffering = Off 
; 输出缓存允许你甚至在输出正文内容之后发送 header（标头，包括cookies）行 
; 其代价是输出层减慢一点点速度。你可以使用输出缓存在运行时打开输出缓存， 
; 或者在这里将指示设为 On 而使得所有文件的输出缓存打开。 
output_handler = ; 你可以重定向你的脚本的所有输出到一个函数， 
; 那样做可能对处理或以日志记录它有用。 
; 例如若你将这个output_handler 设为" ob_gzhandler" , 
; 则输出会被透明地为支持gzip或deflate编码的浏览器压缩。 
; 设一个输出处理器自动地打开输出缓冲。

implicit_flush = Off 
; 强制flush（刷新）让PHP 告诉输出层在每个输出块之后自动刷新自身数据。 
; 这等效于在每个 print() 或 echo() 调用和每个 HTML 块后调用flush()函数。 
; 打开这项设置会导致严重的运行时冲突，建议仅在debug过程中打开。

allow_call_time_pass_reference = On 
; 是否让强迫函数调用时按引用传递参数。这一方法遭到抗议， 
; 并可能在将来版本的PHP/Zend里不再支持。 
; 受到鼓励的指定哪些参数按引用传递的方法是在函数声明里。 
; 你被鼓励尝试关闭这一选项并确认你的脚本仍能正常工作，以保证在将来版本的语言里 
; 它们仍能工作。（你将在每次使用该特点时得到一个警告，而参数将按值而不是按引用 
; 传递）。

; Safe Mode 安全模式 
safe_mode = Off 
safe_mode_exec_dir = 
safe_mode_allowed_env_vars = PHP_ 
; ？Setting certain environment variables 
; ？may be a potential security breach. 
; 该指示包含用逗号分隔的前缀列表。安全模式中，用户仅可以替换 
; 以在此列出的前缀开头的环境变量的值。 
; 默认地，用户将仅能 设定以PHP_开头的环境变量，（如: PHP_FOO=BAR）。 
; 注意: 如果这一指示为空，PHP 将让用户更改任意环境变量!

safe_mode_protected_env_vars = LD_LIBRARY_PATH 
; 这条指示包含一个用逗号分隔的环境变量列表，那是最终用户将不能用putenv () 更改的。 
; 这些变量甚至在safe_mode_allowed_env_vars 设置为允许的情况下得到保护。

disable_functions = 
; 这条指示让你可以为了安全的原因让特定函数失效。 
; 它接受一个用逗号分隔的函数名列表。 
; 这条指示 *不受* 安全模式是否打开的影响。

; 语法高亮模式的色彩。 
; 只要能被接受的东西就能工作。

highlight.string = #DD0000 
highlight.comment = #FF8000 
highlight.keyword = #007700 
highlight.bg = #FFFFFF 
highlight.default = #0000BB 
highlight.html = #000000

; Misc 杂项 
expose_php = Off 
; 决定 PHP 是否标示它装在服务器上的事实（例如：加在它 — PHP— 给Web服务 
; 发送的信号上）。 
; （我个人的意见，在出现什么power-by的header的时候，把这关掉。） 
; 它不会有安全上的威胁, 但它使检查你的服务器上是否安装了PHP成为了可能。


;;;;;;;;;;;;;;;;;;; 
; Resource Limits ; 
;;;;;;;;;;;;;;;;;;;

max_execution_time = 30 ; 每个脚本的最大执行时间, 按秒计 
memory_limit = 8388608 ; 一个脚本最大可使用的内存总量 (这里是8MB)


;;;;;;;;;;;;;;;;;;;;;;;;;;;;;; 
; Error handling and logging ; 
; 出错控制和登记 ; 
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;; 
; 错误报告是按位的。或者将数字加起来得到想要的错误报告等级。 
; E_ALL - 所有的错误和警告 
; E_ERROR - 致命性运行时错 
; E_WARNING - 运行时警告（非致命性错） 
; E_PARSE - 编译时解析错误 
; E_NOTICE - 运行时提醒(这些经常是是你的代码的bug引起的， 
;也可能是有意的行为造成的。(如：基于未初始化的变量自动初始化为一个 
;空字符串的事实而使用一个未初始化的变量)

; E_CORE_ERROR - 发生于PHP启动时初始化过程中的致命错误 
; E_CORE_WARNING - 发生于PHP启动时初始化过程中的警告(非致命性错) 
; E_COMPILE_ERROR - 编译时致命性错 
; E_COMPILE_WARNING - 编译时警告(非致命性错) 
; E_USER_ERROR - 用户产生的出错消息 
; E_USER_WARNING - 用户产生的警告消息 
; E_USER_NOTICE - 用户产生的提醒消息 
; 例子: 
; error_reporting = E_ALL & ~E_NOTICE ; 显示所有的错误，除了提醒 
; error_reporting = E_COMPILE_ERROR|E_ERROR|E_CORE_ERROR ; 仅显示错误 
error_reporting = E_ALL & ~E_NOTICE ; 显示所有的错误，除了提醒 
display_errors = On ; 显示出错误信息(作为输出的一部分) 
; 在最终发布的web站点上，强烈建议你关掉这个特性，并使用 
; 错误日志代替（参看下面）。 
; 在最终发布的web站点继续让 display_errors 有效可能 
; 暴露一些有关安全的信息，例如你的web服务上的文件路径、 
; 你的数据库规划或别的信息。 
display_startup_errors = Off ; 甚至当display_erroes打开了，发生于PHP的启动的步骤中 
; 的错误也不会被显示。 
; 强烈建议保持使 display_startup_errors 关闭， 
; 除了在改错过程中。 
log_errors = Off ; 在日志文件里记录错误（服务器指定的日志，stderr标准错误输出，或error_log(下面的）） 
; 正如上面说明的那样，强烈建议你在最终发布的web站点以日志记录错误 
; 取代直接错误输出。

track_errors = Off ; 保存最近一个 错误/警告 消息于变量 $php_errormsg (boolean) 
;error_prepend_string = " " ; 于错误信息前输出的字符串 
;error_append_string = " " ; 于错误信息后输出的字符串 
;error_log = filename ; 记录错误日志于指定文件 
;error_log = syslog ; 记录错误日志于系统日志 syslog (NT 下的事件日志, Windows 95下无效) 
warn_plus_overloading = Off ; 当将‘ +’ 用于字符串时警告


;;;;;;;;;;;;;;;;; 
; Data Handling ; 
;;;;;;;;;;;;;;;;; 
variables_order = " EGPCS" ; 这条指示描述了PHP 记录 
; GET, POST, Cookie, Environment and Built-in 这些变量的顺序。 
; （以 G, P, C, E & S 代表，通常以 EGPCS 或 GPC 的方式引用）。 
; 按从左到右记录，新值取代旧值。

register_globals = On ; 是否将这些 EGPCS 变量注册为全局变量。 
; 若你不想让用户数据不在全局范围内混乱的话，你可能想关闭它。 
; 这和 track_vars 连起来用更有意义 — 这样你可以通过 
; $HTTP_*_VARS[] 数组访问所有的GPC变量。

register_argc_argv = On ; 这条指示告诉 PHP 是否声明 argv和argc 变量 
; （注：这里argv为数组,argc为变量数） 
; （其中包含用GET方法传来的数据）。 
; 若你不想用这些变量，你应当关掉它以提高性能。

track_vars = On ; 使$HTTP_*_VARS[]数组有效，这里*在使用时用 
; ENV, POST, GET, COOKIE or SERVER替换 
post_max_size = 8M ; PHP将接受的POST数据最大大小。


gpc_order = " GPC" ; 这条指示被人反对。用 variables_order 代替。

; Magic quotes 
magic_quotes_gpc = On ; 在输入的GET/POST/Cookie数据里使用魔术引用 
; （原文就这样，呵呵，所谓magic quotes 应该是指用转义符加在引用性的控制字符上，如 '....） 
magic_quotes_runtime= Off ; 对运行时产生的数据使用魔术引用， 
; 例如：用SQL查询得到的数据，用exec()函数得到的数据，等等 
magic_quotes_sybase = Off ; 采用 Sybase形式的魔术引用（用 '' 脱出 ' 而不用 '）

; 自动在 PHP 文档之前和之后添加文件 
auto_prepend_file = 
auto_append_file =

; 象4.04b4一样，PHP 默认地总是在 “ Content-type:” 头标输出一个字符的编码方式。 
; 让输出字符集失效，只要设置为空。 
; PHP 的内建默认值是 text/html 
default_mimetype = " text/html" 
;default_charset = " iso-8859-1"

;;;;;;;;;;;;;;;;;;;;;;;;; 
; Paths and Directories ; 
;;;;;;;;;;;;;;;;;;;;;;;;; 
include_path = ; include 路径设置，UNIX: " /path1:/path2" Windows: " \path1;\path2" 
doc_root = ; php 页面的根路径，仅在非空时有效 
user_dir = ; 告知 php 在使用 /~username 打开脚本时到哪个目录下去找，仅在非空时有效 
;upload_tmp_dir = ; 存放用HTTP协议上载的文件的临时目录（在没指定时使用系统默认的） 
upload_max_filesize = 2097152 ; 文件上载默认地限制为2 Meg 
extension_dir = c:\php\ ; 存放可加载的扩充库（模块）的目录 
enable_dl = On ; 是否使dl()有效。 
; 在多线程的服务器上 dl()函数*不能*很好地工作， 
; 例如IIS or Zeus，并在其上默认为禁止


;;;;;;;;;;;;;;;; 
; File Uploads ; 
;;;;;;;;;;;;;;;; 
file_uploads = On ; 是否允许HTTP方式文件上载 
;upload_tmp_dir = ; 用于HTTP上载的文件的临时目录（未指定则使用系统默认） 
upload_max_filesize = 2M ; 上载文件的最大许可大小

; Fopen wrappers ; 
;;;;;;;;;;;;;;;;;; 
allow_url_fopen = On ; 是否允许把URLs当作http:.. 或把文件当作ftp:...


;;;;;;;;;;;;;;;;;;;;;; 
; 动态扩展 ; 
; Dynamic Extensions ; 
;;;;;;;;;;;;;;;;;;;;;; 
; 若你希望一个扩展库自动加载，用下面的语法： 
; extension=modulename.extension 
; 例如，在windows上， 
; extension=msql.dll 
; or 在UNIX下, 
; extension=msql.so 
; 注意，这只应当是模块的名字，不需要目录信息放在里面。 
; 用上面的 extension_dir 指示指定扩展库的位置。


;Windows 扩展 
;extension=php_nsmail.dll 
extension=php_calendar.dll 
;extension=php_dbase.dll 
;extension=php_filepro.dll 
extension=php_gd.dll 
;extension=php_dbm.dll 
;extension=php_mssql.dll 
;extension=php_zlib.dll 
;extension=php_filepro.dll 
;extension=php_imap4r2.dll 
;extension=php_ldap.dll 
;extension=php_crypt.dll 
;extension=php_msql2.dll 
;extension=php_odbc.dll 
; 注意， MySQL的支持现在是内建的，因此，不需要用它的dll


;;;;;;;;;;;;;;;;;;; 
; 模块设定 ; 
; Module Settings ; 
;;;;;;;;;;;;;;;;;;;

[Syslog] 
define_syslog_variables = Off ; 是否定义各种的系统日志变量 
; 如：$LOG_PID, $LOG_CRON, 等等。 
; 关掉它是个提高效率的好主意。 
; 运行时，你可以调用函数define_syslog_variables()，来定义这些变量


[mail function] 
SMTP = localhost ;仅用于win32系统 
sendmail_from = me@localhost.com ;仅用于win32系统 
;sendmail_path = ;仅用于unix, 也可支持参数（默认的是'sendmail -t -i'）

[Debugger] 
debugger.host = localhost 
debugger.port = 7869 
debugger.enabled = False

[Logging] 
; 这些配置指示用于示例的日志记录机制。 
; 看 examples/README.logging 以得到更多的解释 
;logging.method = db 
;logging.directory = /path/to/log/directory

[Java] 
;java.class.path = .\php_java.jar 
;java.home = c:\jdk 
;java.library = c:\jdk\jre\bin\hotspot\jvm.dll 
;java.library.path = .\

[SQL] 
sql.safe_mode = Off

[ODBC] 
;uodbc.default_db = Not yet implemented 
;uodbc.default_user = Not yet implemented 
;uodbc.default_pw = Not yet implemented 
uodbc.allow_persistent = On ; 允许或禁止 持久连接 
uodbc.check_persistent = On ; 在重用前检查连接是否还可用 
uodbc.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
uodbc.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制 
uodbc.defaultlrl = 4096 ; 控制 LONG 类型的字段。返回变量的字节数，0 代表通过（？）0 means passthru 
uodbc.defaultbinmode = 1 ; 控制 二进制数据。0 代表?????Handling of binary data. 0 means passthru, 1 return as is, 2 convert to char 
; 见有关 odbc_binmode 和 odbc_longreadlen 的文档以得到 uodbc.defaultlrl 和 uodbc.defaultbinmode 的解释。

[MySQL] 
mysql.allow_persistent = On ; 允许或禁止 持久连接 
mysql.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
mysql.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制 
mysql.default_port = ; mysql_connect() 使用的默认端口，如不设置，mysql_connect() 
; 将使用变量 $MYSQL_TCP_PORT，或在/etc/services 下的mysql-tcp 条目(unix)， 
; 或在编译是定义的 MYSQL_PORT(按这样的顺序) 
; Win32环境，将仅检查MYSQL_PORT。 
mysql.default_socket = ; 用于本地 MySql 连接的默认的套接字名。为空，使用 MYSQL 内建值

mysql.default_host = ; mysql_connect() 默认使用的主机（安全模式下无效） 
mysql.default_user = ; mysql_connect() 默认使用的用户名（安全模式下无效） 
mysql.default_password = ; mysql_connect() 默认使用的密码（安全模式下无效） 
; 注意，在这个文件下保存密码通常是一个*坏*主意 
; *任何*可以使用PHP访问的用户可以运行 
; 'echo cfg_get_var(" mysql.default_password" )'来显示那个密码! 
; 而且当然地，任何有读该文件权力的用户也能看到那个密码。

[mSQL] 
msql.allow_persistent = On ; 允许或禁止 持久连接 
msql.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
msql.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制

[PostgresSQL] 
pgsql.allow_persistent = On ; 允许或禁止 持久连接 
pgsql.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
pgsql.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制

[Sybase] 
sybase.allow_persistent = On ; 允许或禁止 持久连接 
sybase.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
sybase.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制 
;sybase.interface_file = " /usr/sybase/interfaces" 
sybase.min_error_severity = 10 ; 显示的错误的最低严重性 
sybase.min_message_severity = 10 ; 显示的消息的最低重要性 
sybase.compatability_mode = Off ; 与旧版的PHP 3.0 兼容的模式。若打开，这将导致 PHP 自动地 
; 把根据结果的 Sybase 类型赋予它们， 
; 而不是把它们全当成字符串。 
; 这个兼容模式不会永远留着， 
; 因此，将你的代码进行需要的修改， 
; 并将该项关闭。

[Sybase-CT] 
sybct.allow_persistent = On ; 允许或禁止 持久连接 
sybct.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
sybct.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制 
sybct.min_server_severity = 10 ; 显示的错误的最低严重性 
sybct.min_client_severity = 10 ; 显示的消息的最低重要性

[bcmath] 
bcmath.scale = 0 ; 用于所有bcmath函数的10十进制数数字的个数number of decimal digits for all bcmath functions

[browscap] 
;browscap = extra/browscap.ini 
browscap = C:\WIN\SYSTEM\inetsrv\browscap.ini 
[Informix] 
ifx.default_host = ; ifx_connect() 默认使用的主机（安全模式下无效） 
ifx.default_user = ; ifx_connect() 默认使用的用户名（安全模式下无效） 
ifx.default_password = ; ifx_connect() 默认使用的密码（安全模式下无效） 
ifx.allow_persistent = On ; 允许或禁止 持久连接 
ifx.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
ifx.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制 
ifx.textasvarchar = 0 ; 若打开，select 状态符返回一个 ‘ text blob’ 字段的内容，而不是它的id 
ifx.byteasvarchar = 0 ; 若打开，select 状态符返回一个 ‘ byte blob’ 字段的内容，而不是它的id 
ifx.charasvarchar = 0 ; 追踪从固定长度的字符列里剥离的空格。 
; 可能对 Informix SE 用户有效。 
ifx.blobinfile = 0 ; 若打开，text和byte blobs 的内容被导出到一个文件 
; 而不是保存到内存。 
ifx.nullformat = 0 ; NULL（空）被作为空字段返回，除非，这里被设为1。 
; 这种情况下（为1），NULL作为字串NULL返回。

[Session] 
session.save_handler = files ; 用于保存/取回数据的控制方式 
session.save_path = C:\win\temp ; 在 save_handler 设为文件时传给控制器的参数， 
; 这是数据文件将保存的路径。 
session.use_cookies = 1 ; 是否使用cookies 
session.name = PHPSESSID 
; 用在cookie里的session的名字 
session.auto_start = 0 ; 在请求启动时初始化session 
session.cookie_lifetime = 0 ; 为按秒记的cookie的保存时间， 
; 或为0时，直到浏览器被重启 
session.cookie_path = / ; cookie的有效路径 
session.cookie_domain = ; cookie的有效域 
session.serialize_handler = php ; 用于连接数据的控制器 
; php是 PHP 的标准控制器。 
session.gc_probability = 1 ; 按百分比的'garbage collection（碎片整理）'进程 
; 在每次 session 初始化的时候开始的可能性。 
session.gc_maxlifetime = 1440 ; 在这里数字所指的秒数后，保存的数据将被视为 
; '碎片(garbage)'并由gc 进程清理掉。 
session.referer_check = ; 检查 HTTP引用以使额外包含于URLs中的ids无效 
session.entropy_length = 0 ; 从文件中读取多少字节 
session.entropy_file = ; 指定这里建立 session id 
; session.entropy_length = 16 
; session.entropy_file = /dev/urandom 
session.cache_limiter = nocache ; 设为{nocache,private,public},以决定 HTTP 的 
; 缓存问题 
session.cache_expire = 180 ; 文档在 n 分钟后过时 
session.use_trans_sid = 1 ; 使用过渡性的 sid 支持，若编译时许可了 
; --enable-trans-sid 
url_rewriter.tags = " a=href,area=href,frame=src,input=src,form=fakeentry"

[MSSQL] 
;extension=php_mssql.dll 
mssql.allow_persistent = On ; 允许或禁止 持久连接 
mssql.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
mssql.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制 
mssql.min_error_severity = 10 ; 显示的错误的最低严重性 
mssql.min_message_severity = 10 ; 显示的消息的最低重要性 
mssql.compatability_mode = Off ; 与旧版的PHP 3.0 兼容的模式。

[Assertion] 
; ？？？？？ 
;assert.active = On ; ？assert(expr); active by default 
;assert.warning = On ; issue a PHP warning for each failed assertion. 
;assert.bail = Off ; don't bail out by default. 
;assert.callback = 0 ; user-function to be called if an assertion fails. 
;assert.quiet_eval = 0 ; eval the expression with current error_reporting(). set to true if you want error_reporting(0) around the eval().

[Ingres II] 
ii.allow_persistent = On ; 允许或禁止 持久连接 
ii.max_persistent = -1 ; 持久连接的最大数。-1 代表无限制 
ii.max_links = -1 ; 连接的最大数目（持久和非持久）。-1 代表无限制 
ii.default_database = ; 默认 database (format : [node_id::]dbname[/srv_class] 
ii.default_user = ; 默认 user 
ii.default_password = ; 默认 password

[Verisign Payflow Pro] 
pfpro.defaulthost = " test.signio.com" ; 默认的 Signio 服务器 
pfpro.defaultport = 443 ; 连接的默认端口 
pfpro.defaulttimeout = 30 ; 按秒计的默认超时时间

; pfpro.proxyaddress = ; 默认的代理的 IP 地址（如果需要） 
; pfpro.proxyport = ; 默认的代理的端口 
; pfpro.proxylogon = ; 默认的代理的登录（logon 用户名） 
; pfpro.proxypassword = ; 默认的代理的密码

[Sockets] 
sockets.use_system_read = On ; 使用系统的read() 函数替代 php_read()封装 
; Local Variables: （局部变量） 
; tab-width: 4 
; End
```
