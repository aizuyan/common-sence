## laravel源码不完全解读
这里以http为例进行说明

### 
入口文件中首先设置了autoload自动加载，然后就是下面的创建application了:
```php
$app = require_once __DIR__.'/../bootstrap/app.php';
```

`__DIR__.'/..bootstrap/app.php'`这个文件中实例化了一个application（可以理解为容器，他继承自Container），然后注册了3个单例对象，并返回。注册的三个单例对象
```php
$app = new Illuminate\Foundation\Application(
	realpath(__DIR__.'/../');
);
```
这个实例化容器的过程中做了下面几件事情：

1. 将自己绑定到容器中，下面两个都指向容器($this)本身[app, Illuminate\Container\Container] ，并且设置自身的static::$instance职位容器本身。
2. 注册下面两个最近本的服务[EventServiceProvider, RoutingServiceProvider]。
3. 注册核心别名，包括[app, db, log, ......]。
4. 设置application的基本路径basePath，注册相关的路径信息到容器中，包括[path, basePath, configPath, ......]。

注册三个单例类到容器中
```php
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);
$app->singleton(
    Illuminate\Contracts\Console\Kernel::class,
    App\Console\Kernel::class
);
$app->singleton(
    Illuminate\Contracts\Debug\ExceptionHandler::class,
    App\Exceptions\Handler::class
);
```
这里说下容器中的`singleton`这个函数，可以看到singleton只是以共享的方式向容器中绑定一个东东。

然后删除掉之前绑定的相关的单例。

如果没有设置具体的类，默认和抽象的类一样。

接着，如果具体的类不是一个闭包，闭包用来创建具体的类实例。

然后将具体信息放入绑定记录器`bindings`里面，参数是抽象的类名，值是有产生具体对象的闭包(闭包接受两个参数：容器`$c`和参数`$parameters`)和是否共享信息组成的数组。

如果之前已经有实例化过`abstract`的绑定，则重新绑定。
```php
public function singleton($abstract, $concrete = null)
{
    $this->bind($abstract, $concrete, true);
}
$this->dropStaleInstances($abstract);
if (is_null($concrete)) {
    $concrete = $abstract;
}
$this->bindings[$abstract] = compact('concrete', 'shared');
if ($this->resolved($abstract)) {
	$this->rebound($abstract);
}
```


紧接着入口文件中`make`创建一个`Illuminate\Contracts\Http\Kernel::class`对象
```php
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
```
`make`创建实例这个过程比较有意思，我们一起仔细看下
###make创建实例
首先，尝试获取要实例化的类的别名
```php
$abstract = $this->getAlias($abstract);
```
比如说之前给`app`注册了3个别名[Illuminate\Foundation\Application, Illuminate\Contracts\Container\Container, Illuminate\Contracts\Foundation\Application]，但我们获取3个中任意一个的别名的时候都会返回`app`。

下面是一段延时加载的内容，我们在`Illuminate\Foundation\Bootstrap\RegisterProviders::bootstrap()`方法里面将部分需要延时加载的类放在了`$app->deferredServices`数组中，map形式，键是抽象名称，值是具体的类名。

如果要实例化的抽象内容名称在延时加载列表里面，这个时候加载注册这个类，并将其从延时加载注册列表中删除，这样如果就可以获取到延时加载注册的服务了。
```php
if (isset($this->deferredServices[$abstract])) {
	$this->loadDeferredProvider($abstract);
}
```
接下来走到`Container::make`这个用于真正创建实例的函数中，首先还是获取别名（因为这个函数不知在上一步中使用），当然了获取不到，因为上一步已经获取过一次了。

如果单例模式记录器`instances`中有想要获取的内容，直接返回已经创建的单例。

没有的话去获取对应的具体的绑定过的信息，这里目前返回一个闭包。

接着，去判定对象是否可以创建，不能创建的话就递归处理。
```php
if (isset($this->instances[$abstract])) {
	return $this->instances[$abstract];
}
$concrete = $this->getConcrete($abstract);
if ($this->isBuildable($concrete, $abstract)) {
	$object = $this->build($concrete, $parameters);
} else {
	$object = $this->make($concrete, $parameters);
}
```
创建对象的`build`

如果是闭包的话直接返回闭包执行后的结果，如果不是闭包表示是一个类名，这个时候实例化一个该类的`ReflectionClass`类反射实例。

从反射实例中获取类的构造函数，如果没有直接new一个实例返回。

如果有构造函数的话，获取构造函数所依赖的参数，然后对传入的参数进行格式化，将`$parameters`中键名为数字的键名替换为对应的依赖参数`$dependencies`的名称。

然后解决参数的依赖问题，如果参数名在`$parameters`中出现，直接使用，否认，如果不是一个类的话，尝试获取默认值，获取不到实例化失败。如果是一个类的话尝试`make`出来，如果失败，尝试获取默认值，获取不到实例化失败。

如果一切ok，从反射类中实例化出一个具体的实例返回。
```php
if ($concrete instanceof Closure) {
    return $concrete($this, $parameters);
}
$reflector = new ReflectionClass($concrete);
$this->buildStack[] = $concrete;	//TODO 目前还不知道为什么
$constructor = $reflector->getConstructor();
if (is_null($constructor)) {
    array_pop($this->buildStack);

    return new $concrete;
}
$dependencies = $constructor->getParameters();
$parameters = $this->keyParametersByArgument(
    $dependencies, $parameters
);
$instances = $this->getDependencies(
    $dependencies, $parameters
);
array_pop($this->buildStack);
return $reflector->newInstanceArgs($instances);
```


## laravel container中的share
`Illuminate\Routing\RoutingServiceProvider`中的38~40行，这就也绑定了router当容器中，看看这个怎么实现的

在Container中有offsetSet这个函数，这个方法来自`ArrayAccess`这个接口，这个可以让使用者像数组一样访问对象中特定的内容，会触发`offsetSet`这个函数。

`offsetSet`函数这个函数会判断要设置的值是否是闭包，不是的话，包装为闭包，然后绑定到容器中，单是绑定的时候没有设置`shared=true`，为什么还叫`share`?

看看share这个方法，里面设置了个静态变量，当`make`的时候都会执行注册的对应的闭包函数，如果静态变量`$object`已经设置过了的话，就直接返回，和singleton方式的效果一样。

`make`的时候会传入两个参数，这里不会有影响，因为我发现php传入参数可以比定义参数个数多，但不能少。
```php
$this->app['router'] = $this->app->share(function ($app) {
    return new Router($app['events'], $app);
});


public function offsetSet($key, $value)
{
    if (! $value instanceof Closure) {
        $value = function () use ($value) {
            return $value;
        };
    }
    $this->bind($key, $value);
}

public function share(Closure $closure)
{
	return function ($container) use ($closure) {
		static $object;
		if (is_null($object)) {
			$object = $closure($container);
		}
		return $object;
	};
}
```
也就是说，laravel容器中的共享对象有两种定义方式：

1. $app->singleton(...);
2. $app['test'] = $app->share(...);

## bind
这里说下`bind`这个函数，bind只是将一个抽象的类和具体类的实现方法和关联起来，并不会去实例化他，当然如果这个类之前已经实例化了（单例模式或者普通模式），这里也要进行实例化操作。

## 框架何时解析配置文件、注册异常。。。。。。的
创一个kernel之后，调用了kernel的`handle()`方法，这里会去执行数组`$bootstrappers`中每个类的`bootstrap()`方法，这里面就包括了对`.env配置文件`的处理，对普通配置文件的处理，对日志配置的处理，对异常的处理，注册门面注释的配置内容，对服务的注册，处理服务。

在`Illuminate\Foundation\Bootstrap\RegisterProviders::bootstrap()`中有执行了一个重要的方法`$app::registerConfiguredProviders()`，这个方法最后做了这么一个事情，将配置文件`configure('app.providers')`数组中的服务提供类解析了一遍，只注册必须要加载的类到应用中(`protected $defer = false;`)，并且生成`bootstrap\cache\services.json`文件，再次请求的时候就不用再次解析了，当然当配置文件中的`providers`变化时，会更新`bootstrap\cache\services.json`缓存文件。
