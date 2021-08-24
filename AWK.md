### 两行合并为一行
文件 a.txt 内容
```
A
B
C
D
```

```
cat a.txt | awk '{if(NR%2==0){printf $0,"\n"}else{printf "%s：",$0}}'
#输出：
#A：B
#C：D
```

