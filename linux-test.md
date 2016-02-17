# test
test命令在shell中非常有用，经常用于各种各样的逻辑判断。

- 字符串判断
	* `test -z string` 判断字符串是否为空，若字符串为空，则状态码返回`0`。
	* `test -n string` 判断字符串是否非空，若字符串为空，则状态码返回`1`，`-n`参数也可以省略。
	* `test str1 = str2` 判断字符串1和2是否相等，若相等则状态码返回`0`。
	* `test str1 != str2` 判断字符串1和2是否不相等， 若相等则状态码返回`1`
- 整数判断(条件成立状态码返回0)
	* `test n1 -eq n2` 判断两个整数是否相等。
	* `test n1 -ne n2` 判断两个整数是否不相等。
	* `test n1 -gt n2` 判断n1是否大于n2。
	* `test n1 -lt n2` 判断n1是否小于n2。
	* `test n1 -ge n2` 判断n1是否大于等于n2。
	* `test n1 -le n2` 判断n1是否小于等于n2。
- 文件权限(文件权限都是相对于当前用户的，条件成立返回0)
	* `test -r filename` 判断文件是否有`可读`权限。
	* `test -w filename` 判断文件是否有`可写`权限。
	* `test -x filename` 判断文件是否有`可执行`权限。
	* `test -u filename` 判断文件是否有`suid`属性。
	* `test -g filename` 判断文件是否有`sgid`属性。
	* `test -k filename` 判断文件是否有`Sticky bit`属性。
	* `test -s filename` 判断文件是否为非空白文件。
- 文件之间的比较(条件成立返回0)
	* `test file1 -nt file2` 判断file1是否比file2新。
	* `test file1 -ot file2` 判断file1是否比file2旧。
	* `test file1 -ef file2` 判断file1和file2的inode是否相等，我试了下软链接也是返回成功。
- 判断文件是否存在(条件成立返回0)
	* `test -e filename` 判断文件名filename是否存在。
	* `test -f filename` 判断filename是否为文件(file)。
	* `test -d filename` 判断filename是否为目录(directory)。
- 多重判断条件
	* `test -r file -a -x file` file同时具有r和x权限才返回状态码0。
	* `test -r file -o -x file` file具有r或者x权限才返回状态码0。
