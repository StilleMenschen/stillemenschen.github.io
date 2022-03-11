# curl

## 简介

curl 是使用支持的协议之一（DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, MQTT, POP3, POP3S,
RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP）从服务器传输数据或向服务器传输数据的工具。curl 提
供了大量有用的技巧，例如代理支持，用户身份验证，FTP 上传，HTTP 请求，SSL 连接，cookie，文件传输 ​​，Metalink 等

`libcurl`为所有与传输相关的功能提供 curl 支持。有关详细信息，请参见 libcurl(3)

```
curl [options] [URL...]
```

## 链接

`URL`语法与协议有关。您会在[RFC 3986](http://www.ietf.org/rfc/rfc3986.txt)中找到详细的说明。

您可以通过在大括号内编写单词集合，并按如下所示引用 URL 来指定多个 URL 或 URL 的一部分

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

您可以在命令行上指定任意数量的 URL。它们将以指定的顺序以顺序方式获取。您可以在命令行上以任意顺序指定命令行选项和 URL 混
合

您可以为范围指定一个计步器，以获取每个第 N 个数字或字母

```
"http://example.com/file[1-100:10].txt"
"http://example.com/file[a-z:2].txt"
```

当从命令行提示符处调用使用`[]`或`{}`序列时，可能必须将完整的 URL 放在双引号中，以避免 shell 干扰它。其他特殊字符也是如此
，例如`'＆'`，`'？'`和`'*'`

## 输出

如果没有其他说明，curl 将接收到的数据写入`stdout`。可以指示使用`-o`，`--output`或`-O`，`--remote-name`选项将数据保存到本
地文件中。如果为 curl 提供了多个 URL 以便在命令行上进行传输，则类似地，它还需要多个选项来保存它们

curl 不会解析或以其他方式“理解”它作为输出获得或写入的内容。除非使用专用命令行选项明确要求，否则它不会进行编码或解码

## 协议

curl 支持许多协议，或用 URL 术语表示：`schemes`。您的特定内部版本可能无法完全支持它们。

- `DICT` 使您可以使用在线词典查找单词
- `FILE` 读取或写入本地文件。curl 不支持远程访问`file://URL`，但是在 Microsoft Windows 上使用本机 UNC 方法运行时可以使用
- `FTP(S)` curl 通过许多调整和杠杆支持文件传输协议。使用或不使用 TLS
- `GOPHER` 检索文件
- `HTTP(S)` curl 支持具有许多选项和变体的 HTTP。它可以说 HTTP 版本 0.9、1.0、1.1、2 和 3，具体取决于构建选项和正确的命令
  行选项
- `IMAP` 使用邮件阅读协议，curl 可以为您“下载”电子邮件。使用或不使用 TLS
- `LDAP(S)` curl 可以为您执行目录查找，无论是否使用 TLS
- `MQTT` curl 支持 MQTT 版本 3。通过 MQTT 下载等于主题的“订阅”，而上传/发布主题等于“发布”。MQTT 支持是实验性的，尚不支持
  基于 TLS 的 MQTT
- `POP3(S)` 从 pop3 服务器下载意味着收到邮件。使用或不使用 TLS
- `RTMP(S)` 实时消息协议主要用于服务器流媒体，curl 可以下载它
- `RTSP` curl 支持 RTSP 1.0 下载
- `SCP` curl 支持 SSH 版本 2 scp 传输
- `SFTP` curl 支持通过 SSH 版本 2 完成的 SFTP（草案 5）
- `SMB(S)` curl 支持 SMB 版本 1 进行上载和下载
- `SMTP(S)` 将内容上载到 SMTP 服务器意味着发送电子邮件。有或没有 TLS
- `TELNET` 告诉 curl 获取 telnet URL，将启动一个交互式会话，在该会话中它将发送在 stdin 上读取的内容，并输出服务器发送的
  内容
- `TFTP` curl 可以执行 TFTP 下载和上传

## 进度

curl 通常在操作过程中显示进度表，指示传输的数据量，传输速度和剩余的估计时间等。进度表显示字节数，速度以每秒字节数为单位
。后缀（k，M，G，T，P）基于 1024。例如 1k 是 1024 字节。1M 是 1048576 字节

curl 默认情况下会将此数据显示到终端，因此，如果您调用 curl 进行操作并且将要向终端写入数据，它将禁用进度条，否则会混淆输
出混合进度条和响应数据

如果要使用`HTTP` `POST`或`PUT`请求的进度表，则需要使用`shell`重定向（`>`），`-o, --output`或类似方法将响应输出重定向到文
件

FTP 上传的情况不同，因为该操作不会向终端吐出任何响应数据

如果您更喜欢进度条而不是常规的列表，`-#`或`--progress-bar`选项可以改变进度条显示方式。您也可以使用`-s, -silent`选项完全
禁用进度表

## 选项

<style>
table th:first-of-type {
    width: 22%;
}
</style>

| 选项                          | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| :---------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -K, --config `<file>`         | 指定外部选项文件，如果使用`-`，则从输入流中读取选项，选项文件中一个选项一行（如果是双破折号的选项，则可以忽略`--`但指定值必须用`=`号连接，如`url="http://www.example.com"`）                                                                                                                                                                                                                                                                                          |
| --connect-timeout `<seconds>` | 连接超时时间（单位：秒）                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| -c, --cookie-jar `<file>`     | (HTTP)将响应的`cookie`写入一个文件，如果指定`-`，则写入`stdout`中                                                                                                                                                                                                                                                                                                                                                                                                     |
| -b, --cookie `<data\|file>`   | (HTTP)指定发送请求携带的`cookie`，以选项形式指定：`-b "NAME1=VALUE1; NAME2=VALUE2"`，以文件形式指定则每行一个指定一个`key=value`形式的`cookie`                                                                                                                                                                                                                                                                                                                        |
| --data-binary `<data>`        | (HTTP)这将完全按照指定的方式发布数据，而不会进行任何额外处理，如果使用`@`符号，则后面必须跟上明确的文件名                                                                                                                                                                                                                                                                                                                                                             |
| --data-raw `<data>`           | (HTTP)发送原始的请求数据，且不处理`@`符号指定的文件名                                                                                                                                                                                                                                                                                                                                                                                                                 |
| --data-urlencode `<data>`     | (HTTP)发送数据并使用`urlencode`编码，一个`key=value`使用一次该选项，多个则需指定多次，支持的表达式：<br>`content` 将内容编码后发送<br>`=content` 将内容编码后发送，不包含`=`符号<br>`@filename` 将文件内容编码后再发送<br>`name@filename` 将文件内容编码，发送内容并指定`name`值，与`application/x-www-form-urlencoded`的表单类似                                                                                                                                     |
| -d, --data `<data>`           | (HTTP MQTT)将 POST 请求中的指定数据发送到 HTTP 服务器，就像用户填写 HTML 表单并按 Submit 按钮时浏览器所做的一样。这将导致 curl 使用内容类型`application/x-www-form-urlencoded`将数据传递到服务器                                                                                                                                                                                                                                                                      |
| -D, --dump-header `<file>`    | 将服务器响应的标头写入文件                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| --fail-early                  | 当指定多个请求时，将会在第一个请求失败后直接退出并返回，不会再继续执行后续请求                                                                                                                                                                                                                                                                                                                                                                                        |
| --fail-with-body              | (HTTP)如果服务器错误（HTTP 响应代码为 400 或更大），则返回错误。在正常情况下，当 HTTP 服务器无法交付文档时，它会返回 HTML 文档，说明其内容（通常还会说明原因以及更多内容）。此标志仍将允许 curl 输出并保存该内容，而且还返回错误 22                                                                                                                                                                                                                                   |
| -f, --fail                    | (HTTP)服务器错误时静默失败（根本没有输出）。这样做主要是为了使脚本等能够更好地处理失败的尝试。在正常情况下，当 HTTP 服务器无法交付文档时，它会返回 HTML 文档，说明其内容（通常还会说明原因以及更多内容）。该标志将阻止 curl 输出该错误并返回错误 22                                                                                                                                                                                                                   |
| --form-string `<name=string>` | (HTTP SMTP IMAP)与`-F, --form`相似，不同之处在于，原样使用了命名选项的值字符串。 开头的`'@'`和`'<'`字符以及值中的`'; type='`字符串没有特殊含义                                                                                                                                                                                                                                                                                                                        |
| -F, --form <name=content>     | (HTTP SMTP IMAP)对于 HTTP 协议系列，它可以使 curl 模仿用户按下提交按钮时所填写的表单。curl 会以 Content-Type 为`multipart/form-data`发送请求，参考[RFC 2388](http://www.ietf.org/rfc/rfc2388.txt)，示例：<br>传输本地文件（使用`@`）并指定文件类型和文件名<br>`curl -F "profile=@portrait.jpg;type=image/jpeg;filename=person.jpg" https://example.com/upload.cgi`<br>读取本地文件内容作为传输数据（使用`<`）<br>`curl -F "story=<hugefile.txt" https://example.com/` |
| -G, --get                     | 使用此选项时，将使用`-d, --data`，`--data-binary`或`--data-urlencode`指定的所有数据用于 HTTP GET 请求中，而不是用于其他情况的 POST 请求中。数据将以`?`附加到 URL                                                                                                                                                                                                                                                                                                      |
| -I, --head                    | (HTTP FTP) 仅获取响应标头。HTTP 服务器具有命令 HEAD，该命令用于获取文档的标头。当在 FTP 或 FILE 文件上使用时，curl 仅显示文件大小和最后修改时间                                                                                                                                                                                                                                                                                                                       |
| -H, --header `<header/@file>` | (HTTP)自定义请求头，如`curl -H "Authorization: token" https://example.com`，或者使用外部文件`curl -H @content.txt http://example.com`，文件内容：<br>`Host: example.com`<br>`Authorization: 123456`                                                                                                                                                                                                                                                                   |
| -h, --help `<category>`       | 显示帮助信息后退出，如果指定了分类`<category>`，则只显示该分类下的选项说明，支持的分类：`auth`，`connection`，`curl`，`dns`，`file`，`ftp`，`http`，`imap`，`misc`，`output`，`pop3`，`post`，`proxy`，`scp`，`sftp`，`smtp`，`ssh`，`telnet`，`tftp`，`tls`，`upload`，`verbose`                                                                                                                                                                                     |
| -i, --include                 | 在输出中包括 HTTP 响应标头。HTTP 响应标头可以包括服务器名称，Cookie，文档日期，HTTP 版本等等                                                                                                                                                                                                                                                                                                                                                                          |
| -k, --insecure                | (TLS)默认情况下，将验证每个 curl 建立的 SSL 连接的安全性。使用此选项，即使对于服务器连接（如果认为不安全），curl 仍可继续进行和操作                                                                                                                                                                                                                                                                                                                                   |
| --libcurl `<file>`            | 指定文件将输出一段 C 语言的源码，此源码的功能与命令行上的操作相同                                                                                                                                                                                                                                                                                                                                                                                                     |
| --limit-rate `<speed>`        | 限制 curl 的传输速度，如指定`256k`，`3m`，`1G`（单位：千字节`k`或`K`，兆字节`m`或`M`，千兆字节`g`或`G`）                                                                                                                                                                                                                                                                                                                                                              |
| -L, --location                | 跟随`Location`响应头中的`3xx`代码重定向到新地址，当 curl 跟随重定向并且如果请求是 POST 时，如果 HTTP 响应是 301、302 或 303，它将使用 GET 执行以下请求。如果响应代码是任何其他 3xx 代码，curl 将重新发送该请求，使用`-X, --request`设置的方法将覆盖 curl 选择使用的方法                                                                                                                                                                                               |
| -M, --manual                  | 显示帮助手册                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| --max-redirs `<num> `         | 指定跟随重定向的次数（十进制），默认为`50`次，如果需要解除限制可指定为`-1`                                                                                                                                                                                                                                                                                                                                                                                            |
| -m, --max-time `<seconds>`    | 指定完成操作的最大时长                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| -o, --output `<file>`         | 将输出写到`<file>`而不是`stdout`                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| -#, --progress-bar            | 显示操作进度条                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| --raw                         | (HTTP)取消对传输内容的编码和解码                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| -e, --referer `<URL>`         | (HTTP)将`Referrer Page`信息发送到 HTTP 服务器                                                                                                                                                                                                                                                                                                                                                                                                                         |
| -J, --remote-header-name      | (HTTP)此选项告诉`-O, --remote-name`选项使用服务器指定的`Content-Disposition`文件名，而不是从 URL 中提取文件名                                                                                                                                                                                                                                                                                                                                                         |
| -O, --remote-name             | 将输出写入本地文件，该文件名为获得的远程文件。（仅使用远程文件的文件部分，路径被截断）该文件将保存在当前工作目录中。如果要将文件保存在其他目录中，请确保在使用此选项调用 curl 之前更改当前工作目录                                                                                                                                                                                                                                                                    |
| -R, --remote-time             | 使用时，这将使 curl 尝试找出远程文件的时间戳，如果可用，则使本地文件获得相同的时间戳                                                                                                                                                                                                                                                                                                                                                                                  |
| -X, --request `<command>`     | (HTTP)指定与 HTTP 服务器通信时要使用的自定义请求方法。将使用指定的请求方法代替其他方法（默认为 GET）。阅读 HTTP 1.1 规范以获取详细信息和说明。一般情况下不需要明确指明此选项。各种各样的 GET，HEAD，POST 和 PUT 请求都可以通过使用专用的命令行选项来调用。此选项仅更改 HTTP 请求中使用的实际单词，不会更改 curl 的行为方式。例如，如果您想发出适当的 HEAD 请求，则使用-X HEAD 将无法满足要求。您需要使用`-I, --head`选项                                              |
| -S, --show-error              | 与`-s, --silent`一起使用时，如果 curl 失败，它将使 curl 显示一条错误消息                                                                                                                                                                                                                                                                                                                                                                                              |
| -s, --silent                  | 静音或安静模式。不显示进度表或错误消息。使 curl 静音。除非您将其重定向，否则它仍将输出您要求的数据，甚至可能输出到终端/标准输出                                                                                                                                                                                                                                                                                                                                       |
| --create-dirs                 | 当与`-o, --output`选项一起使用时，curl 将根据需要创建必要的本地目录层次结构                                                                                                                                                                                                                                                                                                                                                                                           |
| --ftp-create-dirs             | (FTP SFTP)当 FTP 或 SFTP 操作使用服务器上当前不存在的路径时，curl 的标准行为将失败。使用此选项，curl 会尝试创建不存在的目录                                                                                                                                                                                                                                                                                                                                           |
| --create-file-mode `<mode>`   | (SFTP SCP FILE)当使用 curl 使用支持的协议之一远程创建文件时，此选项允许用户设置在创建时在文件上设置的`mode`（权限），而不是默认的`0644`（`-rw-r--r--`）。此选项将八进制数作为选项                                                                                                                                                                                                                                                                                     |
| -T, --upload-file `<file>`    | 这会将指定的本地文件传输到远程 URL，支持指定多文件，如`curl --upload-file "{file1,file2}" http://www.example.com`或`curl -T "img[1-1000].png" ftp://ftp.example.com/upload/`                                                                                                                                                                                                                                                                                          |
| --url `<url>`                 | 指定请求链接                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| -A, --user-agent `<name>`     | 指定请求头中的`User-Agent`标头                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| -v, --verbose                 | 在操作过程中使卷曲变得冗长                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -V, --version                 | 显示版本号后退出                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

## 命令示例

1. 下载文件，并参考链接中的文件名称存储文件

   ```bash
   curl -O https://www.example.com/file.zip
   ```

2. 通过 sftp 上传文件到服务器`/opt`目录

   ```bash
   curl -T file.zip -u "ftpuser:123456" "sftp://192.168.0.2/opt/"
   ```

3. 发送表单请求且携带有鉴权信息
   ```bash
   curl -X POST -H "Authorization: token" -F "user=black" -F "age=18" -F "picture=@p1.jpg" "https://www.example.com/api/user"
   ```

Last Modified 2021-04-11
