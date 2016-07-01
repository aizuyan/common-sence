### PHP中self、static的区别
self、static都是指向类的本身，但是还有一点区别，self就是指向当前类的本身，static类似于`$this`

### 访问属性
`self`会优先在函数所在的类中查找属性，如果没有，网继承链上一级找
```php
<?php
class A{
	public static $name = "A";
}

class B extends A{
	public static function getName() {
		return self::$name;
	}
}

class C extends A{
	public static $name = "C";
	public static function getName() {
		return self::$name;
	}
}

B::getName();	//可以访问到A::$name
C::getName();	//访问到C::$name
```
```php
<?php
class A{
	public static $name = "A";
	public static function getName() {
		return self::$name;
		//return static::$name;
	}
}

class B extends A{
}

class C extends A{
	public static $name = "C";
}

var_dump(B::getName());	//访问到A::$name
var_dump(C::getName());	//访问到A::$name

//static
//var_dump(B::getName());	//访问到A::$name
//var_dump(C::getName());	//访问到C::$name
```

yii2中的di
```php
<?php
class A{
}
class B extends A{
	public $name = "B";
}

class Instance{
	private $id;
	public function __construct($id) {
		$this->id = $id;
	}

	public static function of($name) {
		return new static($name);
	}
}

var_dump(Instance::of("A"));
var_dump(Instance::of("B"));
```

```php
class P{
	public static function create() {
		#return new static();
		return new self();
	}
}

class Son extends P{

}

var_dump(P::create());	
var_dump(Son::create());
/*
 *object(P)#1 (0) {
 *}
 *object(P)#1 (0) {
 *}
 */

#return new static
/*
 *object(P)#1 (0) {
 *}
 *object(Son)#1 (0) {
 *}
*/

```
