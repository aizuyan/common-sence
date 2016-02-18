# php中的标准输入和输出
## CLI模式下面
在CLI模式下，PHP有三个文件句柄，`STDIN`，`STDOUT`，`STDERR`，分别代表标准输入、标准输出、标准错误，着和linux下面类似。
CLI模式下面运行下面的代码
```php
var_dump(STDIN);
var_dump(STDOUT);
var_dump(STDERR);
```
运行上面的代码会输出下面内容：
```
resource(1) of type (stream)
resource(2) of type (stream)
resource(3) of type (stream)
```

## CGI模式下面（或者CLI模式下）
```php
var_dump(fopen("php://stdin", "r"));
var_dump(fopen("php://stdout", "w"));
var_dump(fopen("php://stderr", "w"));
var_dump(fopen("php://input", "r"));
var_dump(fopen("php://output", "w"));
```
运行上面代码输出：
```
resource(5) of type (stream)
resource(6) of type (stream)
resource(7) of type (stream)
resource(8) of type (stream)
resource(9) of type (stream)
```

## 读写
php://stdin，php://input 是只读的， php://stdout 和 php://stderr，php://output 是只写的
