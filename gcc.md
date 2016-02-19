## gcc编译的时候相关的知识点

#### 预处理
`-E`gcc只进行预处理工作，把程序中的宏展开，把头文件的内容展开。
```shell
gcc -E test.c -o test.i
```
#### 编译为汇编代码
`-S`gcc对程序进行编译，生成汇编代码。
```shell
gcc -S test.i -o test.s
gcc -S test.c -o test.s
```

#### 汇编
`-c`对程序进行汇编处理，生成目标文件
```shell
gcc -c test.s -o test.o
gcc -c test.c -o test.o
```

#### 链接
链接生成可执行文件
```shell
gcc test.o -o test
```

###多个文件例子
```shell
gcc -c test1.c -o test1.o
gcc -c test2.c -o test2.o
gcc test1.o test2.o -o test
```

### 参数`-Wall`
让编译器输出尽可能多的警告信息
```shell
gcc -Wall test.c -o test
```
还有`-Werror`，再有警告的地方停止编译。

### 使用第三方库
编译的时候很多时候会使用第三方库，这样会事半功倍，相当于站在了巨人的肩膀上。 
```shell
#汇编生成.o文件，这里test.c引用了/usr/local/third/include里面的头文件
gcc -c -I /usr/local/third/include test.c -o test.o

#生成可执行文件，需要第三方的类库
gcc -L /usr/local/third/lib -lmyneed test.o -o test
```

