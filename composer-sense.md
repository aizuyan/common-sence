## composer中的配置项
[http://docs.phpcomposer.com](http://docs.phpcomposer.com/)
参考文章[http://docs.phpcomposer.com/04-schema.html](http://docs.phpcomposer.com/04-schema.html)

### name
当前包的名称，包括vender名称和项目名称，通过'/'分隔，是一个完整包所必须的参数。

## description
当前包的简介，是一个完整包所必须的参数。

## version
当前包的版本，可选配置项，大部分时候不要配置最好，因为包仓库可以推断出版本，比如从VCS(版本控制系统)的标签名称中。

## type
- library 这个类型composer会将包拷贝到`vender`目录下面
- project 这个类型表示本包是一个项目不是一个库
- metapackage 

### require模块
这个模块包含了项目所依赖的包名称和版本。

### autoload
自动加载配置，目前这个配置项支持`PSR-0自动加载`，`PSR-4自动加载`，`classmap生成`，`files包含`集中方式。
- PSR-4 在这个key里面包含的是namespace到paths的映射表，namespace前缀必须以`\\`结尾。在install/update的时候PSR-4相关的会自动设置。
- PSR-0 跟上面类似
- Classmap 这里面的所有内容，都会在install/update/dump的时候生成，并存储到`vender/composer/autoload_classmap.php`文件中。可以用classmap生成自定义加载不支持PSR-0/4规范的类库。
- Files 每次请求的时候都要加载的文件可以放在这里，通常是函数库

# composer.lock文件
当composer安装成功之后composer会把安装时确切的版本号列表写入composer.lock文件，这回锁定该项目的特定版本，一定要将composer.json和composer.lock文件提交到版本库中。
install会检查锁文件是否存在，如果存在，他将下载指定的版本而忽略composer.json中的定义。
如果composer.lock文件不存在，composer将会读取composer.json并生成锁文件composer.lock。
当然了这也意味着更新版本之后获取不到，此时请使用update命令进行更新。

# composer中的环境变量
设置composer的环境变量一般在composer.json中的`"config":{}`配置项里面配置，当然也可以直接设置环境变量。环境变量中的值优先级高于config中的配置。

## COMPOSER
```shell
COMPOSER=my-composer.json	//为composer.json指定别名
```

## COMPOSER_VENDOR_DIR
```shell
COMPOSER_VENDOR_DIR=myvender	//指定依赖关系的安装目录，可以安装在vender之外的目录中。
```

## COMPOSER_BIN_DIR
```shell
COMPOSER_BIN_DIR=myvender/bin	//指定vender的bin目录。
```

## http_proxy || HTTP_PROXY
```shell
HTTP_PROXY=http://www.myproxy.com	//设置代理，这里建议同时设置http_proxy
```

## COMPOSER_HOME
用来改变composer的主目录，这是一个隐藏的共享的全局目录。

#composer命令的使用

## `composer update`
```php
composer update nothing //更新composer.lock文件，nothing这里并不是关键字，这里只是没有这个包
composer update 		//根据composer.json更新内容，并更新composer.lock文件
composer update foo/bar	//更新单个库
```

## `composer require`
```php
composer require "foo/bar:1.0.0"	//不用修改composer.json添加一个库
composer init --require=foo/bar:1.0.0 -n	//初始化一个项目，生成composer.json文件，多个的话，需要多次`--require={name}:{version}`
```

## `composer create-project`
```php
composer create-project laravel/laravel /datas/wwwroot/test		//composer create-project [PACKAGE] [DESTINATION PATH] [--FLAGS]
```

## 
```php
composer config --list	// 查看composer的配置内容
```
