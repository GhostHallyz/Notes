nginx  referer模块

配置说明 

| Syntax:  | `**valid_referers** none | blocked | server_names | *string* ...;` |
| :------- | ------------------------------------------------------------ |
| Default: | —                                                            |
| Context: | `server`, `location`                                         |

**none** 不带referer请求头，invalid_referer = ""，不支持单独只配置 none

**blocked** 包含所有不是以 http(s):// 开头的referer请求头（包含 "referer："），invalid_referer = ""，不支持单独只配置 blocked

**server_names** referer请求头包含当前配置的 server name 配置的其中一个，invalid_referer = ""

**任意字符串 \***范匹配 server name 或者 uri 前缀，可以配置在 server name 开头和结尾，invalid_referer = ""，注意：不允许配置重复的 uri 前缀（存在包含的情况）。例如 

```
www.test.com``*.test.com``www.test.*``*.test.*
```

   **正则表达式** 第一个符号应该是“~”。 需要注意的是，表达式将与“http://”或“https://”之后开始的文本进行匹配，匹配时 invalid_referer = ""。需要注意字符转义。

```
valid_referers ~\.baidu\.;
```



注意点：

（1）referer 长度需要大于等于 sizeof("[http://i.ru](http://i.ru/)") - 1 (nginx referer模块对处理的referer请求头有长度限制，未达到长度的一致将 invalid_referer 置为1)

（2）valid_referers 不支持只配置 none 或者 blocked ，加载配置时nginx报错

（3）不支持重复覆盖配置，加载配置时nginx报错，例如

```
valid_referers *.so.com *.so.com/aa;
```

（4）配置不支持 http(s):// ，配置无效，例如

```
valid_referers http:``//www.so.com;
```

（5）配置 blocked 的时候区分 "referer:" 和 "referer:aaa" 的情况。通过set_by_lua处理这部分逻辑

```lua
set_by_lua_block $nilRef 
{
    local ngx_var = ngx.var;
    local ngx_req_set_header = ngx.req.set_header;
    if ngx_var.http_referer == "" then
        ngx_req_set_header("referer", nil);
    end
}
```

