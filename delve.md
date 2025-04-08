## delve 教程

## 列出文件
1. `sources` 列出所有文件完整路径。
2. `funcs` 列出所有函数名。
3. `funcs \.Handle$` 匹配，函数名是Handle的
4. `funcs ListPushTemplateForTikTok` **ListPushTemplateForTikTok** 是类型，匹配类型、函数名中包含该类型名字的。
5. `list path/to/pp.(*ListPushTemplateForTikTok).Handle` 列出函数名所在文件、行。

### 设置变量
1. `set cdnReq["appid"].(int) = 1301652000`，设置 **interface{}** 类型的 **int** 值。
2. `call cdnReq["push_domain"].(string) = "push-rtmp-l11-va01.test.com"`，设置字符串，字符串需要分配空间，因此使用 call，做进一步处理。
