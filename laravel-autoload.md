# Laravel的自动加载机制

## 类库的自动加载
符合composer自动加载规范就可以自动被夹在

## 依赖注入
IoC(Inversion of Control),laravel 中称为服务容器，使用前需要注册服务名和具体的内容到容器中（比如application中的bind、singleton、instance方法），相当于容器中以key=>value的方式保存着许多内容，使用的时候再有app程序去解析对应注册信息（比如app->make['test'],app['test']）。

实现一个注册内容，主要在两个方法中实现（Container中的make和build）,make回去递归解析，如果依赖的内容还是类，会继续去尝试build，比如有些地方的参数，直接是一个类名，make就回去尝试构建这个类实例，如果是单例的话直接返回，否在（非单例、容器中没有注册——此时为函数名）重新build

## 门面模式
门面模式让某些服务可以通过静态方法去调用，这个在laravel中主要是通过__callstatic实现的，再通过服务注册和别名共同作用。
