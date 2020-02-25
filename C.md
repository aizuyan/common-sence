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


【简介】华为私有云部门应届生招聘火热进行中！2.28日截止投递简历，3.1日开始面试！

华为私有云多年来位居中国私有云榜首，作为华为私有云核心产品的FusionSphere在中国Openstack软件市场和中国虚拟化市场双双第一，欢迎同学们加入我们FusionSphere团队！

【投递方法】参考如下链接 https://mp.weixin.qq.com/s/akDYA6U2H_TOMqwBXuj23A

其中第三步使用搜索框搜索并选择“云计算开发工程师”岗位，  
第四步部门选择’Cloud&AI （CloudBU）产品与服务'。 

【投递后】投递后请将个人简历及简历编号（在个人中心的右上角）发送至华为招聘专员邮箱 yanruitao1@huawei.com 

期待您的加入！！！
