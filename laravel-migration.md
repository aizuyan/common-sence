## laravel中的数据迁移
* 数据迁移相关的主要用到了一个命令`php artisan migrate`

* 当初始化的时候运行`php artisan migrate`命令的时候，laravel会在数据库中新建立一张`migrations`数据表，里面会记录哪些脚本已经生成和生成的批次（批次用于`php artisan migrate:rollback`时候使用）

* 新建表使用`php artisan make:migration create_books_table --create=books`
	-生成的文件在database/migrations/目录下面，是一个继承自Migration的类，里面有两个方法`up`和`down`，分别用于'装载'和'卸载'

* 增加字段的时候使用`php artisan make:migration add_votes_to_books_table --table=books`
	-在生成的文件中添加内容，`up`方法中放入'装载'的时候要执行的语句，`down`方法中放入'卸载'的时候要执行的语句。具体的语法可以参考laravel的结构生成器[http://laravel-china.org/docs/5.0/schema](http://laravel-china.org/docs/5.0/schema)

* 数据填充的时候使用`php artisan make:seeder BookTableSeeder`会在`database/seeds/`下面生成一个BookTableSeeder类。
	-注意这个类是没有命名空间的，完成之后还需要运行`composer dump`，让数据装载的时候可以找到该类
	-在run方法里面写入要插入的数据，运行`php artisan db:seed --class=BookTableSeeder`，插入数据成功
