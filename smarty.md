#### smarty
smarty是一个强大的模板，以前一直没有意识到，直到看到百度对他的深度应用。
百度有好多业务用到了PHP，前端使用了FIS工具，后端PHP结合了Smarty，让FIS和Smarty共同完成了前端文件的依赖问题以及前后端分离的问题，让项目开发起来很爽。
这里记录下对Smarty的高级使用

#### Smarty插件 ####
Smarty插件开发很简单，只需要放在Smarty的默认`plugins`目录中，或者自定义的插件目录中，然后按照约定的规则去定义文件名称和函数，就可以使用了，以前一直以为这种特性Smarty是不支持的，看来我还是太年轻了。

##### 函数扩展
```php
/**
 *
 * @param array $params 模板传递到函数的参数都保存在$params数组中
 * @param object $template 这里是smarty模板
 */
smarty_function_name($params, $template);

#例子：
//function.showname.php
<?php
function smarty_function_showname($params, $smarty)
{
        return $params['name'] ." => ". $params['age'];
}

//test.tpl
//这里将会输出 【燕睿涛 => 24】
{showname name="燕睿涛" age="24"}

```
##### 修饰器扩展
```php
/**
 *
 * @param mixed $value 要进行'修饰'的变量
 * @param minxed $param1 传递的参数
 */
 smarty_modifier_name($value, [$param1, ...]);

 #例子：
 //modifier.second_up.php
 <?php

 function smarty_modifier_second_up($string)
 {
         return $string[0] . strtoupper($string[1]) . substr($string, 2);
 } 

//test.tpl
//这里输出【yAnruitao】
{"yanruitao"|second_up}
```

##### 块函数
块函数的形式是`{func} ... {/func}`，块函数优先于同名自定义函数
```php
/**
 *
 * @param array $params 传递的参数
 * @param minxed $content 标签之间的内容
 * @param object $template smarty实例
 * @param boolean $repeat 标记是否是开始标签或者结束标签
 */
smarty_block_name($params, $content, $template, &$repeat);

#例子
//block.split_up.php
<?php
function smarty_block_split_line($params, $content, $smarty, &$repeat)
{
    if($repeat)
    {
        return "我的自定义块标签开始了\n";
    }
    else
    {   
        return implode("\n", explode(" ", $content));
    }
} 

//test.tpl
//输出：
我的自定义块标签开始了
/**
 *  Hello
 *  World
 *  Y
 *  R
 *  T
 *  !!
 *  
 *  HHH
 *  WHAT
 */
{split_line}
Hello World Y R T !!

HHH WHAT
{/split_line}

```

##### 编译函数
编译函数只会在模板的编译过程中调用，优先级高于同名的自定义函数。
该函数的返回值将会嵌入到模板PHP代码中
```php
smarty_compiler_name($params, $smarty);

#例子：
//compiler.widget.php
<?php
function smarty_compiler_widget($arrParams, $smarty)
{
    return json_encode($arrParams);
}
//test.tpl
//这里将会输出【{"name":"\"What\/is\"","page":"\"123\"","age":"\"yanruitao\""}】
{widget name="What/is" page="123" age="yanruitao"}
```

TODO 资源类型插件
