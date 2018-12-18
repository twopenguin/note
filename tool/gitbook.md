# 常见报错

在使用`gitbook serve` 命令的时候报如下错误：

`Error: ENOENT: no such file or directory, stat 'XXXX\note\_book\gitbook\gitbook-plugin-fontsettings\fontsettings.js' `

解决办法：

在书的根目录下面建一个`book.json`文件导入如下内容：

```json
{
        "plugins": [
                "fontsettings",
                "sharing",
                "lunr",
                "search",
                "highlight",
                "livereload"
        ]
}
```

然后在`gitbook install`,之后再`gitbook serve`

