# curl

## 简介

curl是使用支持的协议之一（DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, MQTT, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP）从服务器传输数据或向服务器传输数据的工具。curl提供了大量有用的技巧，例如代理支持，用户身份验证，FTP上传，HTTP请求，SSL连接，cookie，文件传输​​，Metalink等

`libcurl`为所有与传输相关的功能提供curl支持。有关详细信息，请参见libcurl(3)

## 链接

`URL`语法与协议有关。您会在[RFC 3986](http://www.ietf.org/rfc/rfc3986.txt)中找到详细的说明。

您可以通过在大括号内编写单词集合，并按如下所示引用URL来指定多个URL或URL的一部分
```
"http://site.{one,two,three}.com"
```
或者您也可以使用`[]`来获取字母数字系列的序列
```
"ftp://ftp.example.com/file[1-100].txt"
"ftp://ftp.example.com/file[001-100].txt" (前导数字零)
"ftp://ftp.example.com/file[a-z].txt"
```
不支持嵌套序列，但是您可以将多个序列彼此相邻使用
```
"http://example.com/archive[1996-1999]/vol[1-4]/part{a,b,c}.html"
```
您可以在命令行上指定任意数量的URL。它们将以指定的顺序以顺序方式获取。您可以在命令行上以任意顺序指定命令行选项和URL混合

您可以为范围指定一个计步器，以获取每个第N个数字或字母
```
"http://example.com/file[1-100:10].txt"
"http://example.com/file[a-z:2].txt"
```
当从命令行提示符处调用使用`[]`或`{}`序列时，可能必须将完整的URL放在双引号中，以避免shell干扰它。其他特殊字符也是如此，例如`'＆'`，`'？'`和`'*'`

## 输出

如果没有其他说明，curl将接收到的数据写入`stdout`。可以指示使用`-o`，`--output`或`-O`，`--remote-name`选项将数据保存到本地文件中。如果为curl提供了多个URL以便在命令行上进行传输，则类似地，它还需要多个选项来保存它们

curl不会解析或以其他方式“理解”它作为输出获得或写入的内容。除非使用专用命令行选项明确要求，否则它不会进行编码或解码

## 协议

curl支持许多协议，或用URL术语表示：`schemes`。您的特定内部版本可能无法完全支持它们。

- `DICT` 使您可以使用在线词典查找单词
- `FILE` 读取或写入本地文件。curl不支持远程访问`file://URL`，但是在Microsoft Windows上使用本机UNC方法运行时可以使用
- `FTP(S)` curl通过许多调整和杠杆支持文件传输协议。使用或不使用TLS
- `GOPHER` 检索文件
- `HTTP(S)` curl支持具有许多选项和变体的HTTP。它可以说HTTP版本0.9、1.0、1.1、2和3，具体取决于构建选项和正确的命令行选项
- `IMAP` 使用邮件阅读协议，curl可以为您“下载”电子邮件。使用或不使用TLS
- `LDAP(S)` curl可以为您执行目录查找，无论是否使用TLS
- `MQTT` curl支持MQTT版本3。通过MQTT下载等于主题的“订阅”，而上传/发布主题等于“发布”。MQTT支持是实验性的，尚不支持基于TLS的MQTT
- `POP3(S)` 从pop3服务器下载意味着收到邮件。使用或不使用TLS
- `RTMP(S)` 实时消息协议主要用于服务器流媒体，curl可以下载它
- `RTSP` curl支持RTSP 1.0下载
- `SCP` curl支持SSH版本2 scp传输
- `SFTP` curl支持通过SSH版本2完成的SFTP（草案5）
- `SMB(S)` curl支持SMB版本1进行上载和下载
- `SMTP(S)` 将内容上载到SMTP服务器意味着发送电子邮件。有或没有TLS
- `TELNET` 告诉curl获取telnet URL，将启动一个交互式会话，在该会话中它将发送在stdin上读取的内容，并输出服务器发送的内容
- `TFTP` curl可以执行TFTP下载和上传

## 进度

curl通常在操作过程中显示进度表，指示传输的数据量，传输速度和剩余的估计时间等。进度表显示字节数，速度以每秒字节数为单位。后缀（k，M，G，T，P）基于1024。例如1k是1024字节。1M是1048576字节

curl默认情况下会将此数据显示到终端，因此，如果您调用curl进行操作并且将要向终端写入数据，它将禁用进度条，否则会混淆输出混合进度条和响应数据

如果要使用`HTTP` `POST`或`PUT`请求的进度表，则需要使用`shell`重定向（`>`），`-o, --output`或类似方法将响应输出重定向到文件

FTP上传的情况不同，因为该操作不会向终端吐出任何响应数据

如果您更喜欢进度条而不是常规的列表，`-#`或`--progress-bar`参数可以改变进度条显示方式。您也可以使用`-s, -silent`选项完全禁用进度表

## 参数

参数 | 说明
:--- | :---
-K, --config `<file>`         | 指定外部参数文件，如果使用`-`，则从输入流中读取参数，参数文件中一个参数一行（如果是双破折号的参数，则可以忽略`--`但指定值必须用`=`号连接，如`url="http://www.example.com"`）
--connect-timeout `<seconds>` | 连接超时时间（单位：秒）
-c, --cookie-jar `<file>`     | (HTTP)将响应的`cookie`写入一个文件，如果指定`-`，则写入`stdout`中
-b, --cookie `<data\|file>`   | (HTTP)指定发送请求携带的`cookie`，以参数形式指定：`-b "NAME1=VALUE1; NAME2=VALUE2"`，以文件形式指定则每行一个指定一个`key=value`形式的`cookie`
--data-binary `<data>`        | (HTTP)这将完全按照指定的方式发布数据，而不会进行任何额外处理，如果使用`@`符号，则后面必须跟上明确的文件名
--data-raw `<data>`           | (HTTP)发送原始的请求数据，且不处理`@`符号指定的文件名
--data-urlencode `<data>`     | (HTTP)发送数据并使用`urlencode`编码，一个`key=value`使用一次该参数，多个则需指定多次，支持的表达式：<br>`content` 将内容编码后发送<br>`=content` 将内容编码后发送，不包含`=`符号<br>`@filename` 将文件内容编码后再发送<br>`name@filename` 将文件内容编码，发送内容并指定`name`值，与`application/x-www-form-urlencoded`的表单类似
-d, --data `<data>`           | (HTTP MQTT)将POST请求中的指定数据发送到HTTP服务器，就像用户填写HTML表单并按Submit按钮时浏览器所做的一样。这将导致curl使用内容类型`application/x-www-form-urlencoded`将数据传递到服务器
-D, --dump-header `<file>`    | 将服务器响应的标头写入文件
--fail-early                  | 当指定多个请求时，将会在第一个请求失败后直接退出并返回，不会再继续执行后续请求
--fail-with-body              | (HTTP)如果服务器错误（HTTP响应代码为400或更大），则返回错误。在正常情况下，当HTTP服务器无法交付文档时，它会返回HTML文档，说明其内容（通常还会说明原因以及更多内容）。此标志仍将允许curl输出并保存该内容，而且还返回错误22
-f, --fail                    | (HTTP)服务器错误时静默失败（根本没有输出）。这样做主要是为了使脚本等能够更好地处理失败的尝试。在正常情况下，当HTTP服务器无法交付文档时，它会返回HTML文档，说明其内容（通常还会说明原因以及更多内容）。该标志将阻止curl输出该错误并返回错误22
--form-string `<name=string>` | (HTTP SMTP IMAP)与`-F, --form`相似，不同之处在于，原样使用了命名参数的值字符串。 开头的`'@'`和`'<'`字符以及值中的`'; type='`字符串没有特殊含义
-F, --form <name=content>     | (HTTP SMTP IMAP)对于HTTP协议系列，它可以使curl模仿用户按下提交按钮时所填写的表单。curl会以Content-Type为`multipart/form-data`发送请求，参考[RFC 2388](http://www.ietf.org/rfc/rfc2388.txt)，示例：<br>传输本地文件（使用`@`）并指定文件类型和文件名<br>`curl -F "profile=@portrait.jpg;type=image/jpeg;filename=person.jpg" https://example.com/upload.cgi`<br>读取本地文件内容作为传输数据（使用`<`）<br>`curl -F "story=<hugefile.txt" https://example.com/`
-G, --get                     | 使用此选项时，将使用`-d, --data`，`--data-binary`或`--data-urlencode`指定的所有数据用于HTTP GET请求中，而不是用于其他情况的POST请求中。数据将以`?`附加到URL
-I, --head                    | (HTTP FTP) 仅获取响应标头。HTTP服务器具有命令HEAD，该命令用于获取文档的标头。当在FTP或FILE文件上使用时，curl仅显示文件大小和最后修改时间
-H, --header `<header/@file>` | (HTTP)自定义请求头，如`curl -H "Authorization: token" https://example.com`，或者使用外部文件`curl -H @content.txt http://example.com`，文件内容：<br>`Host: example.com`<br>`Authorization: 123456`
-h, --help `<category>`       | 显示帮助信息后退出，如果指定了分类`<category>`，则只显示该分类下的参数说明，支持的分类：`auth`，`connection`，`curl`，`dns`，`file`，`ftp`，`http`，`imap`，`misc`，`output`，`pop3`，`post`，`proxy`，`scp`，`sftp`，`smtp`，`ssh`，`telnet`，`tftp`，`tls`，`upload`，`verbose`
-i, --include                 | 在输出中包括HTTP响应标头。HTTP响应标头可以包括服务器名称，Cookie，文档日期，HTTP版本等等
-k, --insecure                | (TLS)默认情况下，将验证每个curl建立的SSL连接的安全性。使用此选项，即使对于服务器连接（如果认为不安全），curl仍可继续进行和操作
--libcurl `<file>`            | 指定文件将输出一段C语言的源码，此源码的功能与命令行上的操作相同
--limit-rate `<speed>`        | 限制curl的传输速度，如指定`256k`，`3m`，`1G`（单位：千字节`k`或`K`，兆字节`m`或`M`，千兆字节`g`或`G`）
-L, --location                | 跟随`Location`响应头中的`3xx`代码重定向到新地址，当curl跟随重定向并且如果请求是POST时，如果HTTP响应是301、302或303，它将使用GET执行以下请求。如果响应代码是任何其他3xx代码，curl将重新发送该请求，使用`-X, --request`设置的方法将覆盖curl选择使用的方法
-M, --manual                  | 显示帮助手册
--max-redirs `<num> `         | 指定跟随重定向的次数（十进制），默认为`50`次，如果需要解除限制可指定为`-1`
-m, --max-time `<seconds>`    | 指定完成操作的最大时长
-o, --output `<file>`         | 将输出写到`<file>`而不是`stdout`
-#, --progress-bar            | 显示操作进度条
--raw                         | (HTTP)取消对传输内容的编码和解码
-e, --referer `<URL>`         | (HTTP)将`Referrer Page`信息发送到HTTP服务器
-J, --remote-header-name      | (HTTP)此选项告诉`-O, --remote-name`选项使用服务器指定的`Content-Disposition`文件名，而不是从URL中提取文件名
-O, --remote-name             | 将输出写入本地文件，该文件名为获得的远程文件。（仅使用远程文件的文件部分，路径被截断）该文件将保存在当前工作目录中。如果要将文件保存在其他目录中，请确保在使用此选项调用curl之前更改当前工作目录
-R, --remote-time             | 使用时，这将使curl尝试找出远程文件的时间戳，如果可用，则使本地文件获得相同的时间戳
-X, --request `<command>`     | (HTTP)指定与HTTP服务器通信时要使用的自定义请求方法。将使用指定的请求方法代替其他方法（默认为GET）。阅读HTTP 1.1规范以获取详细信息和说明。一般情况下不需要明确指明此参数。各种各样的GET，HEAD，POST和PUT请求都可以通过使用专用的命令行参数来调用。此参数仅更改HTTP请求中使用的实际单词，不会更改curl的行为方式。例如，如果您想发出适当的HEAD请求，则使用-X HEAD将无法满足要求。您需要使用`-I, --head`参数
-S, --show-error              | 与`-s, --silent`一起使用时，如果curl失败，它将使curl显示一条错误消息
-s, --silent                  | 静音或安静模式。不显示进度表或错误消息。使curl静音。除非您将其重定向，否则它仍将输出您要求的数据，甚至可能输出到终端/标准输出
--create-dirs                 | 当与`-o, --output`选项一起使用时，curl将根据需要创建必要的本地目录层次结构
--ftp-create-dirs             | (FTP SFTP)当FTP或SFTP操作使用服务器上当前不存在的路径时，curl的标准行为将失败。使用此参数，curl会尝试创建不存在的目录
--create-file-mode `<mode>`   | (SFTP SCP FILE)当使用curl使用支持的协议之一远程创建文件时，此选项允许用户设置在创建时在文件上设置的`mode`（权限），而不是默认的`0644`（`-rw-r--r--`）。此选项将八进制数作为参数
-T, --upload-file `<file>`    | 这会将指定的本地文件传输到远程URL，支持指定多文件，如`curl --upload-file "{file1,file2}" http://www.example.com`或`curl -T "img[1-1000].png" ftp://ftp.example.com/upload/`
--url `<url>`                 | 指定请求链接
-A, --user-agent `<name>`     | 指定请求头中的`User-Agent`标头
-v, --verbose                 | 在操作过程中使卷曲变得冗长
-V, --version                 | 显示版本号后退出

Last Modified 2021-04-08
