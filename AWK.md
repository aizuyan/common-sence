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

###  正则替换
```
| 0-1s    |  67303 |
| 1-2s    | 116139 |
| 2-3s    | 159859 |
| 3-4s    |  66329 |
```

```
awk -F '|'  'BEGIN{print "["} {sub(/^ +/, "", $2);sub(/ +$/, "", $2);gsub(/ /, "", $3);print "\{name:\""$2"\",value:\""$3"\"\},"} END{print "]"}' t.txt
# 输出：
[
{name:"0-1s",value:"67303"},
{name:"1-2s",value:"116139"},
{name:"2-3s",value:"159859"},
{name:"3-4s",value:"66329"},
]
```
