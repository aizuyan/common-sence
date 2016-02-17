# autoconf
## AC_ARG_ENABLE
```autoconf
#AC_ARG_ENABLE(feature,help-string [,action-if-given [,action-if-not-given]]);
#编译的时候指定--enable-feature，执行action-if-given，否则执行action-if-not-given
AC_ARG_ENABLE(yaf-debug,
[  --enable-yaf-debug     Enable yaf debug mode default=no],
[PHP_YAF_DEBUG=$enableval],
[PHP_YAF_DEBUG="no"]);

#在php源码中，对AC_ARG_ENABLE进行了一次封装，为PHP_ARG_ENABLE，他会自动生成PHP_$feature变量`PHP_[]translit($1,a-z0-9-,A-Z0-9_)`，而不需要手动指定。
```

## AC_DEFINE
```autoconf
#AC_DEFINE(variable, value,[description])
#声明一个宏定义
AC_DEFINE(PHP_YAF_DEBUG,1,[define to 1 if you want to change the POST/GET by php script])
```

## AC_MSG_CHECKING
```autoconf
#AC_MSG_CHECKING (feature-description)
#输出信息，'feature-description'前添加'checking '，后面添加' ...'
AC_MSG_CHECKING([PHP version])
#输出 checking PHP version ...
```

## AC_MSG_RESULT
```autoconf
#AC_MSG_RESULT(result-description)
#输出结果，之前应该有一个AC_MSG_CHECKING
AC_MSG_CHECKING([PHP version])
AC_MSG_RESULT(yes)
#输出 checking PHP version ... yes
```

## AC_MSG_NOTICE
输出notice信息，如果运行时加了`--quiet`或者`--silent`参数则不显示
```autoconf
AC_MSG_NOTICE([checking if stack overflow is detectable])
```

## AC_MSG_ERROR
输出标准错误信息并退出configure，返回状态码（默认为1），错误描述应该以小写字母开头
```autoconf
#AC_MSG_ERROR (error-description, [exit-status])
AC_MSG_ERROR([php-config not found])
```
## ifelse
格式：`ifelse(string1, string2, equal, not-equal)`
如果string1和string2相等执行equal，否则执行not-equal。


## AC_DEFUN
定义一个autoconf宏，格式`AC_DEFUN(name, [body])`
```autoconf
dnl php编译的时候添加构建目录的函数PHP_ADD_BUILD_DIR
AC_DEFUN([PHP_ADD_BUILD_DIR],[
  ifelse($2,,[
    BUILD_DIR="$BUILD_DIR $1"
  ], [
    $php_shtool mkdir -p $1
  ])
])
dnl 定义一个autoconf宏`PHP_ADD_BUILD_DIR`，宏可以带参数，第一个传进去为`$1`，第二个为`$2`，以此类推。
```

