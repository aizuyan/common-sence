#nginx中的常用
* `pid /usr/local/nginx/nginx.pid` 设置进程id存储文件
* `user www www` 设置运行nginx的用户和用户组
* worker_cpu_affinity，将进程和cpu核数进行绑定。例如4核机器，2个进程：`worker_proceses 2; worker_cpu_affinity 0101 1010;`
* add_header添加头信息 => `add_header name value;`，eg：`add_header Content-Disposition "attachment;filename=test.txt";`
* worker_priority，worker进程的nice优先级。取值范围-20~+19，-20最高，+19最小，在*inx操作系统中，当许多进程处于可执行状态时，将选择所有进程的优先级决定本次选择执行哪一个进程。进程所分配的时间片大小也与进程优先级相关，优先级越高分配的时间片也就越大（默认情况，最小时间片5ms，最大800ms）。因此，优先级越高的进程占有更多的系统资源。
* worker_rlimit_nofile，worker进程最大打开文件数量，有效修复“too many open files”问题，可以设置大于`ulimit -a`的数量。
* worker_rlimit_sigpending，设定发往worker进程队列的数量限制。
* alias，这个指令和root类似。
* internal，指定某个location只能被“内部的”请求调用，外部的调用请求会返回“Not found”(404)。

## nginx error_page
```nginx
error_page   404          /404.html;

error_page   502 503 504  /50x.html;
error_page   403          http://example.com/forbidden.html;
error_page   404          = @fetch;	//重新定向到一个location
error_page 404 =200 /.empty.gif;	//修改HTTP返回的status
error_page   404  =  /404.php;		//`=`将404.php执行之后的status返回
error_page   404  /404.php;			//不返回
```

##nginx location
    location [=|~|~*|^~|@] pattern {
        ......
    }

    [=|~|~*|^~|@] 被称作location修正符(location modifier)，用来定义Nginx如何去匹配其后的pattren，以及该pattern的最基本的属性（简单字符串或正则表达式）。
### =
完全匹配，而且这里的pattern只能是简单的字符串，不能使用正则表达式，匹配上之后停止搜索，因此对一个经常出现的uri定义这个，会在一定程度上加快速度。
Exmple：
    
    server {
        server_name website.com;
        location = /abcd {
            ......
        }
    }
上面是一个完全匹配(=)的例子，看看他匹配那些内容：

    http://website.com/abcd                     # 正好完全匹配
    http://website.com/ABCD                     # 如果运行 Nginx server 的系统本身对大小写不敏感，比如 Windows ，那么也匹配
    http://website.com/abcd?param1=123       # 忽略查询串参数（query string arguments），这里就是 /abcd 后面的 ?param1=123
    http://website.com/abcd/                    # 不匹配，因为末尾存在反斜杠（trailing slash），Nginx 不认为这种情况是完全匹配
    http://website.com/abcde                    # 不匹配，因为不是完全匹配

### (None)
可以不写location modifier，nginx仍然会去匹配以指定的pattern开头的URI，这里的URI只能是普通字符串，不能使用正则表达式。
Example:

    server {
        server_name test.com;
        location /abcd {
            ......
        }
    }

可以匹配的URI是=修正符加上下面的情况：
    
    http://test.com/abcd/                    # 匹配
    http://test.com/abcd/cfg                 # 匹配
    http://website.com/abcd/                 # 匹配 
    http://website.com/abcde                 # 仍然匹配，因为 URI 是以 pattern 开头的

### ~
这个location modifier对大小写敏感，pattern必须为正则表达式
Example:

    server {
        server_name test.com;
        location ~ ^/abcd$ {
            ......
        }
    }

匹配情况：

    http://test.com/abcd    #匹配
    http://test.com/ABcd    #不匹配，大小写敏感
    http://test.com/abcd/   #不匹配

### ~*
这个location modifier和~类似，但是不区分大小写
Example:

    server {
        server_name test.com;
        location ~* ^/abcd$ {
            ......
        }
    }

可以匹配和~类似的情况，还有下面的：

    http://test.com/AbCd    #匹配，大小写不敏感

### ^~
匹配和(None)类似类似的情况，以指定匹配模式开的的URI被匹配，一旦匹配，停止寻找其他模块的location

### @
用于定义一个location块，且该块不能被外部的client访问，只能被nginx内部配置指令访问

## 优先级顺序
每个server里面的location块的次序是不重要的，Nginx会根据location mofifier的优先级依次用URI去匹配pattern，顺序如下：
* =
* (None)    如果pattern完全匹配URI
* ^~
* ~或~*
* (None)    pattern匹配URI的头部(找到最佳)

## upstream
```nginx
upstream server_tips_all{
    server 192.168.0.1:9000 wight=3 max_fails=20 fail_timeout=20s;  //权重为3，失败20次之后，摘掉该机器，20秒之后再次尝试

    keepalive 16;       //这里指的是每个worker连接后端的最大长连接数
}
```
使用的时候很简单：`proxy_pass http://server_tips_all`。

## nginx 常用常识
```nginx
set $var "hello World";
set $var1 what;
```



## 参考文献
[Nginx的Location指令配置](http://blog.chinaunix.net/uid-20788470-id-3080834.html)

http://www.php100.com/html/webkaifa/HTML5/2012/0831/10979.html
