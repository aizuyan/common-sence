## composer中的配置项
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
