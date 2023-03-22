# 字符串变量替换

```java
package tech.tystnad;

import java.util.Map;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class Program {
    public static void main(String[] args) {
        Map<String, String> stringMap = Map.of("address", "127.0.0.0", "port", "3306", "database", "sample");
        String source = "jdbc:mysql://{address}:{port}/{database}?useUnicode=true&characterEncoding=utf8&useSSL=false";
        Pattern pattern = Pattern.compile("\\{([^/]+?)}");
        Matcher matcher = pattern.matcher(source);
        StringBuilder sb = new StringBuilder();
        while (matcher.find()) {
            String varName = matcher.group(1);
            Object varValue = stringMap.get(varName);
            if (null == varValue) {
                continue;
            }
            String formatted = varValue.toString();
            formatted = Matcher.quoteReplacement(formatted);
            matcher.appendReplacement(sb, formatted);
        }
        matcher.appendTail(sb);
        System.out.println(sb);
    }
}
```

>基于`OpenJDK 17`

Last Modified 2023-03-22
