# 函数与变量

- `${__threadNum}` 获取线程号
- `${__threadGroupName}` 获取线程组名称
- `${__samplerName()}` 获取采样器名称
- `${__TestPlanName}` 获取测试计划名称
- `${__machineIP}` 获取当前机器 IP 地址
- `${__machineName}` 获取当前机器名称
- `${__time()}` 获取当前时间戳，如`${__time(YMD,)}`

  更多格式可参考[java.text.SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)
  ，特殊格式表示

  ```
  YMD = yyyyMMdd
  HMS = HHmmss
  YMDHMS = yyyyMMdd-HHmmss
  ```

- `${__timeShift(dd/MM/yyyy,21/01/2018,P2D,,)}` 以给定格式返回日期，并添加指定的秒数，分钟数，小时数，天数或月数

  - `PT20.345S` 解析为 `20.345` 秒
  - `PT15M` 解析为 `15` 分钟
  - `PT10H` 解析为 `10` 小时
  - `P2D` 解析为 `2` 天
  - `-P6H3M` 解析为 `-6` 小时和 `-3`分钟

  > 详细可参考[java.time.Duration](https://docs.oracle.com/javase/8/docs/api/java/time/Duration.html)

- `${__XPath(/path/to/build.xml, //target/@name)}` XPath 函数读取 XML 文件并匹配 XPath。每次调用该函数时，将返回下一个匹
  配项。在文件末尾，它将绕回开始。如果没有匹配的节点，则该函数将返回空字符串，并且警告消息将写入 JMeter 日志文件

- `${__FileToString(D:/file.txt,)}` 读取文件内容
- `${__StringToFile(D:/file.txt,string\n,true)}` 将字符串写入文件，第三个参数表示是否追加内容到已有文件，第四个参数表示
  文件的字符编码，默认 UTF-8
- `${__counter(false, my_counter)}` 计数器，返回一个从 1 开始计算的数，第一个参数表示是否在所有线程中共享计数器，第二个
  参数表示赋值给一个变量

- `${__dateTimeConvert(01212018,MMddyyyy,dd/MM/yyyy,)}` 以指定格式转换时间，如果第二个参数不指定，则认为第一个参数是毫秒
  表示，如`${__dateTimeConvert(1526574881000,,dd/MM/yyyy HH:mm,)}`
- `${__digest(SHA-256,Felix qui potuit rerum cognoscere causas,mysalt,,)}` 加密字符串，第一个参数为加密方式，支持（MD2
  MD5 SHA-1 SHA-224 SHA-256 SHA-384 SHA-512），第二个参数为待加密的字符串，第三个参数为盐值（支持的加密方式需要），第四
  个参数是转换大写
- `${__intSum(1,2,5,${MYVAR})}` 32 位整数求和，最后一个参数表示赋值变量
- `${__longSum(1,2,5,${MYVAR})}` 64 位整数求和，最后一个参数表示赋值变量
- `${__Random(0,10, MYVAR)}` 返回指定范围的随机数并赋值给变量，如果忽略变量名则只返回结果
- `${__RandomDate(d/M/yyyy,,8/7/2050,,)}` 生成随机的日期时间，第一个参数表示格式化字符串，第二个参数表示开始时间（默认当
  前时间），第三个参数表示结束时间
- `${__RandomString(10,abcdefg)}` 参考给定的字符串，生成指定数量的随机字符串
- `${__UUID()}` 返回一个 UUID
- `${__isPropDefined(START.MS)}` 判断属性是否存在
- `${__property(user.dir)}` 返回 jmeter 的属性值，第二个参数可以指定将获得的值赋给自定义变量
- `${__P(ThreadGroup1.threads)}` 简化的`__property`
- `${__setProperty(var1, abc123)}` 设置一个属性值，这个是 jmeter 全局属性，可以在线程和线程组中共享
- `${__split(${val3},val_t,&)}` 以指定的分隔符拆分字符串并赋值给变量

  假设变量`val3`的值为`aa&bb&cc&dd`，使用该函数拆分`${__split(${val3},val_t,&)}`，可以获得 5 个变量，分别
  为`val_t_1=aa`、`val_t_2=bb`、`val_t_3=cc`、`val_t_4=dd`、`val_t_n=4`

- `${__eval(${val})}` 解析变量中的变量，如`${val}`的值为`This is a ${name}`，`${name}`的值为`Tom`，使用 eval
  后`${val}`将会被解析为`This is a Tom`

- `${__evalVar(val)}` 与`__eval`类似，但不需要写变量符号包裹`${}`
- `${__V(This is a ${name})}` 解析参数字符串中的变量，替换实际的变量值后返回
- `${__char(65,66)}` 将数字转为实际的`Unicode`字符，也支持十六进制和八进制的字符，
  如`${__char(0x41, 0x42)}`、`${__char(0101, 0102)}`
- `${__changeCase(abc,UPPER,)}`、`${__changeCase(ABC,LOWER,)}`、`${__changeCase(abc,CAPITALIZE,)}`转换字母大写、小写和首
  字母大写
- `${__escapeHtml("bread" & "butter")}` 使用 HTML 实体对字符串中的字符进行转义的函数。支持 HTML 4.0 实体
- `${__escapeOroRegexpChars([^"].+?,)}` 转义 `ORO Regexp`元字符的函数，等效于 Java Regexp Engine 中的`\Q\E`
- `${__escapeXml("bread" & 'butter')}` 使用 XML 1.0 实体对字符串中的字符进行转义的函数
- `${__unescapeHtml(&lt;Fran&ccedil;ais&gt;)}` 将包含 HTML 实体转义符的字符串取消转义为包含与转义符相对应的实际 Unicode
  `${__urldecode(Word+%22school%22+is+%22%C3%A9cole%22+in+french)}` 解码`application/x-www-form-urlencoded`字符串的函数
  。 注意：使用 UTF-8 作为编码方案
- `${__urlencode(Word "school" is "école" in french)}` 将字符串编码为`application/x-www-form-urlencoded`字符串的函数

Last Modified 2021-05-19
