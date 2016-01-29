# Laravel的自动加载机制

## 类库的自动加载
符合composer自动加载规范就可以自动被夹在

## 依赖注入
IoC(Inversion of Control),laravel 中称为服务容器，使用前需要注册服务名和具体的内容到容器中（比如application中的bind、singleton、instance方法），相当于容器中以key=>value的方式保存着许多内容，使用的时候再有app程序去解析对应注册信息（比如app->make['test'],app['test']）。

实现一个注册内容，主要在两个方法中实现（Container中的make和build）,make回去递归解析，如果依赖的内容还是类，会继续去尝试build，比如有些地方的参数，直接是一个类名，make就回去尝试构建这个类实例，如果是单例的话直接返回，否在（非单例、容器中没有注册——此时为函数名）重新build

## 门面模式
门面模式让某些服务可以通过静态方法去调用，这个在laravel中主要是通过__callstatic实现的，再通过服务注册和别名共同作用。

## 自动加载代码分析
入口文件中有加载`require __DIR__.'/../bootstrap/autoload.php';`这个文件是用来加载vender自动加载函数的。

上面的文件里面有下面几句，首先声明框架的开始时间，然后加载vender中的用于注册自动加载的文件。
```php
define('LARAVEL_START', microtime(true));
require __DIR__.'/../vendor/autoload.php';
$compiledPath = __DIR__.'/cache/compiled.php';
if (file_exists($compiledPath)) {
    require $compiledPath;
}
```

看看`vender/composer/autoload_real.php`这个文件，这个文件有个很奇怪的类，名字是类似这样的`ComposerAutoloaderInit...`，其实这个名字是在composer中生成的，后面的一串字母加数字首次是通过`md5(uniqid('', true))`生成的。

注册自动加载主要执行了函数`ComposerAutoloaderInit...::getLoader()`这个函数，这个函数实例化了一个`vender/composer/ClassLoader.php`这个类，通过这个实例可以完成框架的自动加载，他可以实现PSR-0, PSR-4 and classmap的自动加载。

首先注册命名空间和目录的对应关系，这里注册的是否和psr-0的

当(!$namespace)为真的时候注册的path为psr-0全局的。
```php
$map = require __DIR__ . '/autoload_namespaces.php';
foreach ($map as $namespace => $path) {
    $loader->set($namespace, $path);
}
```

接着注册符合PSR-4的自动加载，其实就是注册一些命名空间和目录的对应关系

同样psr-4也可以注册全局的查找目录。
```php
$map = require __DIR__ . '/autoload_psr4.php';
foreach ($map as $namespace => $path) {
    $loader->setPsr4($namespace, $path);
}
```

接着，向`$loader`中注册classmap格式的类，当实例化一个还没有加载的类的时候，这种格式优先级最高，速度也最快。

最后加载插件自定义的函数或者注册自定义自动加载函数。
```php
$classMap = require __DIR__ . '/autoload_classmap.php';
if ($classMap) {
    $loader->addClassMap($classMap);
}

$loader->register(true);

$includeFiles = require __DIR__ . '/autoload_files.php';
foreach ($includeFiles as $fileIdentifier => $file) {
    composerRequire285ae83ac4327cf78565d6b0d0cf9ceb($fileIdentifier, $file);
}
```

准备完成之后就去注册自动加载函数，在`ClassLoader::register()`函数中，第二个参数是设置为出错的时候抛出异常，最后一个参数我们这里设置为true，将注册的函数到注册函数队列最前面。
```php
public function register($prepend = false)
{
    spl_autoload_register(array($this, 'loadClass'), true, $prepend);
}
```

`loadClass`函数中主要去查找要加载的类，查找到之后去加载，看看是如何查找的。

首先去注册的`命名空间 => 文件`map中朝找，如果找到对应文件返回并包含到框架中。
```php
if (isset($this->classMap[$class])) {
    return $this->classMap[$class];
}
```

当在classMap中没找到的时候会去注册的命名空间中去查找，首先去PSR-4注册的命名空间下面去查找。

根据类的第一个字符，获取所有的注册的命名空间的前缀和长度，如果前缀和要实例化的类前面完全重合（有可能第一个字母一样，后面不一样的情况），判断将前缀替换为对应路径的文件是否存在，存在则返回。

接着去全局注册的`命名空间=>目录`中去查找，查找到之后返回。
```php
// PSR-4 lookup
$logicalPathPsr4 = strtr($class, '\\', DIRECTORY_SEPARATOR) . $ext;

$first = $class[0];
if (isset($this->prefixLengthsPsr4[$first])) {
    foreach ($this->prefixLengthsPsr4[$first] as $prefix => $length) {
        if (0 === strpos($class, $prefix)) {
            foreach ($this->prefixDirsPsr4[$prefix] as $dir) {
                if (file_exists($file = $dir . DIRECTORY_SEPARATOR . substr($logicalPathPsr4, $length))) {
                    return $file;
                }
            }
        }
    }
}

// PSR-4 fallback dirs
foreach ($this->fallbackDirsPsr4 as $dir) {
    if (file_exists($file = $dir . DIRECTORY_SEPARATOR . $logicalPathPsr4)) {
        return $file;
    }
}
```

如果PSR-4规范没有找到在PSR-0中查找

PSR-0会把`'\\'`最后面一段名称中的`_`替换为目录分割符号，如果没有`'\\'`的话，直接把名称中的`_`替换为目录分隔符。

这里要注意，不会替换命名空间前缀，而是直接拼接。

如果文件存在则返回。

如果没找到会去注册的全局目录中查找，如果还没找到，回去php系统配置的查找目录中去查找。
```php
// PSR-0 lookup
if (false !== $pos = strrpos($class, '\\')) {
    // namespaced class name
    $logicalPathPsr0 = substr($logicalPathPsr4, 0, $pos + 1)
        . strtr(substr($logicalPathPsr4, $pos + 1), '_', DIRECTORY_SEPARATOR);
} else {
    // PEAR-like class name
    $logicalPathPsr0 = strtr($class, '_', DIRECTORY_SEPARATOR) . $ext;
}

if (isset($this->prefixesPsr0[$first])) {
    foreach ($this->prefixesPsr0[$first] as $prefix => $dirs) {
        if (0 === strpos($class, $prefix)) {
            foreach ($dirs as $dir) {
                if (file_exists($file = $dir . DIRECTORY_SEPARATOR . $logicalPathPsr0)) {
                    return $file;
                }
            }
        }
    }
}

// PSR-0 fallback dirs
foreach ($this->fallbackDirsPsr0 as $dir) {
    if (file_exists($file = $dir . DIRECTORY_SEPARATOR . $logicalPathPsr0)) {
        return $file;
    }
}

if ($this->useIncludePath && $file = stream_resolve_include_path($logicalPathPsr0)) {
    return $file;
}
```
