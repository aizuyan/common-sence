### LESS
>LESS是一种动态样式语言，属于CSS预处理语言的一种，它使用类似CSS的语法，为CSS赋予了动态语言的特性，如变量、继承、运算、函数等，更方便CSS的编写和维护。

#### 变量
```less
@red: #E10602;
@blue: #2932E1;
@yellow: #CAA066;

.name{
    color: @yellow;
    .first{
        color: @red;
    }
    .second{
        color: @blue;
    }
}
```

编译之后为：
```css
.name {
  color: #CAA066;
}
.name .first {
  color: #E10602;
}
.name .second {
  color: #2932E1;
}
```
变量甚至可以在定义的时候使用其他变量的值
```less
@content: '.';
@pseudo: 'content';
clearfix:after{
  content: @@pseudo;
}
```
输出css：
```css
clearfix:after {
  content: ".";
}
```

变量中的作用域，当前作用域中最后一次的定义将被使用，和css的机制是类似的。
```less
@var: 0;
html{
    @var: 1;
    body{
        @var: 2;
        div: @var;
        @var: 3;
    }
    div: @var;
}
```
编译处理之后为：
```css
html {
  div: 1;
}
html body {
  div: 3;
}
```
### 混合
混合——通俗的将有点像php中的Trait，会将一段定义好的样式代码直接复制到使用的地方
```less
.clearfix-after{
    content: '.';
    height: 0;
    display: block;
    clear: both;
    overflow: hidden;
}
#flow-left{
    flow: left;
}
.show-name{
    color: red;
    width: 100px;
    .clearfix-after;
    #flow-left;
}
```
编译之后的css：
```css
.clearfix-after {
  content: '.';
  height: 0;
  display: block;
  clear: both;
  overflow: hidden;
}
.show-name {
  color: red;
  width: 100px;
  content: '.';
  height: 0;
  display: block;
  clear: both;
  overflow: hidden;
  flow: left;
}
```
但是这样会将不想要的`.clearfix-after`、`#flow-left`也引到样式文件中，污染了样式表，可以通过在样式名后面加双括号让编译器知道他不是一个正常的样式`.clearfix-after()`。当然了，混合中还可以使用参数以及默认值`.clearfix-after(@x: 0, @y: 0, @blur: 1px, @color: #000)`，使用的时候`.clearfix-after(2px 5px);`，

```less
.box-shadow(@x: 0,@y: 0, @blur: 1px, @color: #000){
  box-shadow: @arguments;
  -moz-box-shadow: @arguments;
  -webkit-box-shadow: @arguments;
}
.box-shadow(2px, 5px);
```
输出
```css
box-shadow: 2px 5px 1px #000;
-moz-box-shadow: 2px 5px 1px #000;
 -webkit-box-shadow: 2px 5px 1px #000;
```
@arguments——`@arguments`表示所有的参数。
@rest——`@rest`表示从自己开始的所有参数。
!important——`!important`会给混合中的所有属性都标记为`!important`。
