#### ZEND中config.m4配置常用的一些宏
```m4
AC_MSG_CHECKING(message)
dnl 执行configure命令的时候输出"checking `message` ..."这样的信息，不会自动换行。

AC_MSG_RESULT(value)
dnl 表示输出执行结果，一般为"`value`"，输出结果最后回家上换行。

AC_MSG_ERROR(message)
dnl 执行configure脚本的术后输出错误消息"configure: error: `message`"，并停止脚本的运行。
```

下面是configure中的代码，和执行时候的显示内容：

![AC_MSG相关宏的代码](https://github.com/aizuyan/images/blob/master/common-sence/sh.png)

![AC_MSG相关宏的执行结果](https://github.com/aizuyan/images/blob/master/common-sence/shret.png)

```m4
AC_DEFINE(name, value, description)
dnl 像config.h中添加一条宏定义：#define name value  //description是注释说明
```

下面是configure中的代码和结果：
`AC_DEFINE([YAN_RUITAO], 1, [燕睿涛的名字])`

```c
/* 燕睿涛的名字 */
#define YAN_RUITAO 1
```

```m4
PHP_ADD_INCLUDE(path [,before])
dnl 添加path到头文件的搜索路径，如果设置了before，会将搜索路径添加到所有路径的最前面
```

下面是一个小例子：
```m4
PHP_ADD_INCLUDE([/tmp/pinyin])
dnl 输出结果为 `INCLUDES = -I/usr/local/php/include/php -I/usr/local/php/include/php/main -I/usr/local/php/include/php/TSRM -I/usr/local/php/include/php/Zend -I/usr/local/php/include/php/ext -I/usr/local/php/include/php/ext/date/lib -I/tmp/pinyin`
dnl 可以看到搜索头文件的路径最后添加了我们的目标文件夹

PHP_ADD_INCLUDE([/tmp/pinyin], 1)
dnl 输出结果为`INCLUDES = -I/tmp/pinyin -I/usr/local/php/include/php -I/usr/local/php/include/php/main -I/usr/local/php/include/php/TSRM -I/usr/local/php/include/php/Zend -I/usr/local/php/include/php/ext -I/usr/local/php/include/php/ext/date/lib`
dnl 上面的这个效果和不带第二个参数的类似，只不过将目标路径加载了最前面
```

```m4
PHP_ARG_ENABLE(pinyin, whether to enable pinyin support,
[  --enable-pinyin           Enable pinyin support])

dnl 第一个参数是扩展的名称，第二个参数当我们运行./configure脚本的时候显示的内容，最后一个参数是我们调用./configure --help的时候显示的帮助信息
```


还有一个比较有用的是`ifelse`，这个是autotools中的内容：
```m4
ifelse(string1, string2, equal, not-equal)
dnl 如果string1和string2想等执行equal的内容，否则执行not-equal的内容
```

```m4
AC_CACHE_VAL (cache-id, commands-to-set-it)
确认由cache-id指定的检查的结果是可用的。如果检查的结果在读入的缓存文件中，并且configure 没有用`--quiet'或者`--silent'调用，就打印一条消息以说明该结果已经被缓存了；否则，就运行 shell命令commands-to-set-it。这些命令不应具有副作用，但设置变量cache-id除外。它们尤其不应该调用 AC_DEFINE；紧随与对AC_CACHE_VAL的调用之后的代码应该根据缓存的值调用AC_DEFINE 作这件事。此外，它们不应该打印任何消息，比如说使用AC_MSG_CHECKING；应该在调用AC_CACHE_VAL 之前打印，以便不论测试的结果是从缓存中检索而得到的，还是通过运行shell命令而确定的，都会打印消息。如果是运行 shell命令以确定值，该值将在configure创建它的输出文件之前被储存到缓存文件中。

AC_CACHE_CHECK (message, cache-id, commands)
这是一个更详尽地处理了打印消息的AC_CACHE_VAL版本。本宏为这些宏的最常见的应用提供了便捷的缩写。 它为message调用AC_MSG_CHECKING，而后以cache-id和commands为参数 调用AC_CACHE_VAL，最后以cache-id为参数调用AC_MSG_RESULT。
```
