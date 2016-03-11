#### C中的指针常量和常量指针

#### 指针常量
指针是一个常量，指针指向的内容不是常量，可以这样记忆：
`*` => 指针，`const` => 常量
```c
char *msg = "Hello World!!";
char * const p = &msg;
p++;    //这里会报出错误。
```

#### 常量指针
常量的指针，指针指向的内容是常量，可以这么记忆：
`const` => 常量，`*` => 指针
```c
char *msg = "Hello World!!";
char const *p = &msg;
*p = "WHAT";  //报出错误
```
