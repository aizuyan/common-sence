## laravel数据库使用中的一些基础内容

###
* Eloquent会假设模型对应的数据表为模型表明的小写的复数形式，例如User模型对应的表默认为users，可以通过`proected $table = 'my_user'`属性重写。
* Eloquent还会假设表的主键为id，可以通过属性`protected $primaryKey = 'my_primary_key'`进行重写。
* Eloquent还可以对默认数据库连接进行重写`protected $connection = 'mysql2';`
* Eloquent在默认情况下需要数据库有`created_at`和`updated_at`两个字段，如果不想设定或者不想自动更新这两个字段，可以设置`protected $timestamps = false`
* 使用模型的时候可以指定使用哪个数据库`User::on('mysql2')`
* 如果配置了读取写入连接，可以强制使用强制用写库进行查询`User::onWriteConnection()`。
* fillable字段定义了哪些字段支持批量赋值，下面是一个生成`protected $protected $fillable = []`的生成脚本
```sh
#!/bin/bash
H=0
U=0
P=0
D=0
T=0
while getopts "h:u:p:d:t:" opt; do
    case $opt in
        h)
            H=$OPTARG
            ;;
        u)
            U=$OPTARG
            ;;
        p)
            P=$OPTARG
            ;;
        d)
            D=$OPTARG
            ;;
        t)
            T=$OPTARG
            ;;
        \?)
            echo "usage : -h 127.0.0.1 -u root -p 123456 -d database -t users"
            exit 1
            ;;
    esac
done
if [ "$H" = 0 -o "$U" = 0 -o "$P" = 0 -o "$F" = 0 ]
    then
    echo "usage : -h 127.0.0.1 -u root -p 123456 -d database -t users"
    exit 1
fi

MYSQL="mysql -h${H} -u${U} -p${P} --default-character-set=utf8 -A -N"
result=$($MYSQL -e "desc ${D}.${T}")
echo -e "$result" | awk 'BEGIN{ret="protected $table = [";}{ret=ret"'\''"$1"'\'',"}END{len=length(ret) - 1;ret=substr(ret, 0, len)"];";print ret;}'
```
* blade引擎中使用`{{}}`包含php内容，而且都会被转移，如果不想被转义，这里使用`{!!}`，显示花括号用`@{{}}`
