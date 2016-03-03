#### LESS中的知识点

##### 变量
less中的变量很简单，但有一点要注意，变量中的名字可以是另一个变量，比如下面的这段less会输出`content: "yanruitao";`。还有一点要注意，less中的变量为完全的·常量·，只能定义一次。
```less
@name: "yanruitao";
@var: "name";
content: @@var;
```
```less
@color: #123ABC;	//定义一个变量@color

#header {
	color: @color;
}
h1 {
	color: @color;
}
```
上面的less脚本会生成下面的css
```css
#header {
	color: #123ABC;
}
h1 {
	color: #123ABC;
}
```

##### 混合
混合的就是把一个已经存在的类元素加到其他元素中，如果你不想这个class暴露在css中可以这样，在不想显示的类名后面加`()`，表明这是一个类函数，参数个数为0
```less
.bordered () {
	border: 1px solid black;
}
#header {
	.bordered
}
```
```less
.rounded-corners (@radius: 5px) {
	border-radius: @radius;
	-webkit-border-radius: @radius;
	-moz-border-radius: @radius;
}
#header {
	.rounded-corners;
}
#footer {
	.rounded-corners(10px);
}
```
上面的less编译之后的css为
```css
#header {
	border-radius: 5px;
	-webkit-border-radius: 5px;
	-moz-border-radius: 5px;
}
#footer {
	border-radius: 10px;
	-webkit-border-radius: 10px;
	-moz-border-radius: 10px;
}
```

###### 混合中的mixin
混合中还有一个`mixin`，满足条件的就会执行：
```less
.mixin (dark, @color) {
  color: darken(@color, 10%);
}
.mixin (light, @color) {
  color: lighten(@color, 10%);
}
.mixin (@_, @color) {	//第一个参数不进行校验，任意值都行
  display: block;
}
.class {
	.mixin(light, #888);
}
```
上面的`.class` 满足第二个mixin和默认的mixin所以会输出
```css
.class {
	color: #a2a2a2;
	display: block;
}
```
mixin还可以对参数个数进行匹配，有点像其他语言中的重载：
```less
.mixin (@a) {
  color: @a;
}
.mixin (@a, @b) {
  color: fade(@a, @b);
}
```
除此之外，还可以对参数的值进行判断：
```less
**.mixin (@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin (@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin (@a) {
  color: @a;
}**
```
还有一点要注意，less中的关键字`true`，他只表示布尔值：
```less
//下面两个语句是相同的的
.truth (@a) when (@a) { ... }
.truth (@a) when (@a = true) { ... }

//下面这条不会匹配上面的任何一条，出关键字`true`以外的值都被视为布尔假。
.class {
  .truth(40); 
}
```
多个判断条件用关键字`,`分割，和`and`类似：
```less
.mixin (@a) when (@a > 10), (@a < -10) { ... }
```
对参数还可以使用下面的函数进行判断，常用的有：`iscolor, isnumber, isstring, iskeyword, isurl, ispiexl, ispercentage, isem`

##### 嵌套
```less
#header {
	h1 {
		font-size: 26px;
		font-weight: bold;
	}
	p {
		font-size: 12px;
		a {
			text-decoration: none;
			&:hover {
				border-width: 1px;
			}
		}
	}
}
```
上面这段less有多层嵌套，编译成css之后是，这里要注意下`&`这个符号，放在前面不表示嵌套，表示为后面的是css伪选择器，常用的有`hover,active,after,before......`
```css
#header h1 {
	font-size: 26px;
	font-weight: bold;
}
#header p {
	font-size: 12px;
}
#header p a {
	text-decoration: none;
}
#header p a:hover {
	border-width: 1px;
}
```

#####  函数 & 运算
```less
@the-border: 1px;
@base-color: #111;
@red: #842210;
#header {
	color: @base-color * 3;
	border-left: @the-border;
	border-right: @the-border * 2;
}
#footer {
	color: @base-color + #003300;
	border-color: desaturate(@red, 10%);
}
```
上面包含了运算和函数的less文件可以编译为：
```css
#header {
	color: #333;
	border-left: 1px;
	border-right: 2px;
}
#footer {
	color: #114411;
	border-color: #7d2717;
}
```


> 2016年3月3日17:45:19
