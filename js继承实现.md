## 引言
此文章只是记录，网上类似文章已有很多，为了加深记忆，自己在整理写一遍

## 原型链继承
```js
/**
 * 继承中的父类
 * @param {string} name 动物名称
 */
function Animal(name) {
	this.name = name || "Animal";
	this.sleep = () => {
		console.log(this.name + "正在睡觉!")
	}
}
Animal.prototype.eat = function(food) {
	console.log(this.name + "正在吃：" + food)
}
```

> tips：原型链上面使用箭头函数要谨慎

比如我们定义下面的方法，当运行在实例上运行`drink()`的时候，会输出`正在喝水`，name 找不到，因为箭头函数中的 this 指向定义时候的环境，这里指向了 window 。
```js
Animal.prototype.drink = () => {
	console.log(this)
	console.log(this.name + "正在喝水")
}
```

我们来实现一个 Cat 类继承 Animal，Cat 的原型链指向了一个 Animal 实例，当我们产生一个 Cat 实例的时候，Cat 就可以沿着隐式原型链 __proto__ 访问到 Animal 实例中的方法属性，以及 Animal 实例隐式原型链上的方法属性。
```js
function Cat() {

}
Cat.prototype = new Animal()
Cat.prototype.name = "Cat"
```

> 思考 已经实例化的一个子类对象，在父类对象原型链添加属性、方法，请求在子类实例中是否可以访问到？答案是：可以访问到。

> tips 隐式原型链 __proto__ 其实会指向生成该实例对象的原型 prototype。

可以通过 instanceof 判断实例和类的关系，下面两个都会返回 true，这里就涉及到了 [instanceof 的实现](https://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/index.html)。
```js
let a = new Cat()
a instanceof Cat
a instanceof Animal
```
instanceof 里面其实是做下面的操作，a.__proto__ == Cat.prototype，Cat.prototype.__proto__ == Animal，所以两个都是 true。
```js
function instance_of(L, R) {//L 表示左表达式，R 表示右表达式
 var O = R.prototype;// 取 R 的显示原型
 L = L.__proto__;// 取 L 的隐式原型
 while (true) { 
   if (L === null) 
     return false; 
   if (O === L)// 这里重点：当 O 严格等于 L 时，返回 true 
     return true; 
   L = L.__proto__; 
 } 
}
```

> 问题 原型链继承方式比较简单，但是无法向父类构造函数传递参数；子类新增原型链属性、方法，必须在实例化父类（Cat.prototype = new Animal()）对象之后；父类的实例方法属性被子类实例共享。

## 构造继承
> 核心 使用父类的构造函数增强子类实例，复制父类的实例属性给子类，忽略原型属性。

```js
function Cat(name) {
	Animal.call(this)
	this.name = name || "Cat";
}
```

这种方法我感觉比较弱，虽说子类不再共享父类实例属性、方法了，但父类的原型属性、方法、继承关系都没了。下面分别返回 true、false。根据前面的分析不难理解，a.__proto__ == Cat.prototype，Cat.prototype.__proto__ == Object.prototype，Object.prototype.__proto__ == null，所以不是Animal的实例。
```js
let a = new Cat()
a instanceof Cat
a instanceof Animal
```

> 思考，这种方法继承的属性、方法必须在父类的实例属性、方法中，所以子类实例都不会共享，比较费内存，影响性能，继承关系也断了。

## 实例继承

> 核心 为父类实例添加新属性，作为子类实例返回

```js
function Cat(name) {
	let instance = new Animal()
	instance.name = name || "Cat"

	return instance
}
```

> 问题 这种继承方式感觉也很奇葩，子类的实例通过 instanceof 运算竟然返回 false，子类的实例访问不到子类的原型链，这算哪门子继承。

```js
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // false
```

## 拷贝继承

> 核心 将父类中的属性、方法全部拷贝到子类原型链上

> 问题 如果拷贝的父类属性是引用类型的，子类会和父类共享，修改子类，父类也受影响。

## 组合继承

> 核心 调用父类构造函数，继承父类实例属性；父类实例对象作为子类原型，继承父类原型属性。

```js
function Cat(name) {
	Animal.call(this, name)
}
Cat.prototype = new Animal()
Cat.prototype.constructor = Cat
```

> 注意 这里对 Cat 的 constructor 进行了修正，这里有什么用呢，查了很多资料，下面这种情况就会用到，有没有`Cat.prototype.constructor = Cat`影响很大，有的话，函数加到Cat原型链，没有加到Animal原型链。

```js
let a = new Cat("🐈")
let b = new Cat("🐶")
a.constructor.prototype.say = function() {
	console.log("汪汪汪、喵喵喵")
}
```

> tips 这个方法继承已经比较完善了，继承关系，复用都保留了。只是调用了2次父类构造函数，父类实例属性及存在于子类实例中，也存在子类原型链中。

## 寄生组合继承
```js
function Cat(name, age) {
	Animal.call(this, name)
	this.age = age
}
Cat.prototype = Object.create(Animal.prototype)
Cat.prototype.constrctor = Cat
```

> 问题 为什么不直接把 Cat.prototypee 指向Animal.prototype？答案是肯定的，如果指向的话修改 Cat.prototype 会影响父类原型链，通过 Object.create 生成一个对象，该对象的原型链指向 Animal.prototype

关于函数 Object.create 的内容可以看[这里](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

Object.create 内部其实是下面这样的
```js
function create(obj) {
	function F(){}
	F.prototype = obj
	return new F()
}
```

> tips 这个继承方法应该是最完善最好的了，避免了 组合继承 的构造函数调用两次的问题。
