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

### 分组统计(group by)

```
183.38.20, 43, 4.732004187681845
183.38.35, 21, 3.7352626621723175
183.6.112, 57, 11.232897851202223
183.6.163, 31, 20.667687500676802
183.63.119, 44, 22.997924521388043
183.63.214, 33, 18.33403893187642
185.115.4, 15, 6.158904527376095
```

```
awk -F ',' '{latencyMs = ($3 * 1000); if (latencyMs == 0) {items["0s"]++} else if (latencyMs <= 1000) {items["(0,1]s"]++} else if (latencyMs <= 2000) {items["(1,2]s"]++}else if (latencyMs <= 3000) {items["(2,3]s"]++}else if (latencyMs <= 4000) {items["(3,4]s"]++}else if (latencyMs <= 5000) {items["(4,5]s"]++}else if (latencyMs <= 6000) {items["(5,6]s"]++}else if (latencyMs <= 7000) {items["(6,7]s"]++}else if (latencyMs <= 8000) {items["(7,8]s"]++}else if (latencyMs <= 9000) {items["(8,9]s"]++}else if (latencyMs <= 10000) {items["(9,10]s"]++} else {items["(10,100]s"]++}}  END{for (i in items) {print i","items[i]}}' ret.txt

# 输出：
(6,7]s,1
(10,100]s,4
(4,5]s,1
(3,4]s,1
```
