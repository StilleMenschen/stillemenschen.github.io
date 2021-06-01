# BeanShell

BeanShell 是一个小型的、免费的、可嵌入的 Java 源代码解释器，具有对象脚本语言特性，用 Java 编写。 它在 Java 运行时环境
(JRE) 中运行，动态执行标准 Java 语法并使用常见的脚本编写便利对其进行扩展，例如松散类型、命令和方法闭包，如 Perl 和
JavaScript 中的那些。

```
foo = "Foo";
four = (2 + 2)*2/2;
print( foo + " = " + four );  // print() is a BeanShell command

// Do a loop
for (i=0; i<5; i++)
    print(i);

// Pop up a frame with a button in it
button = new JButton( "My Button" );
frame = new JFrame( "My Frame" );
frame.getContentPane().add( button, "Center" );
frame.pack();
frame.setVisible(true);
```

## 方法

```
int addTwoNumbers( int a, int b ) {
    return a + b;
}

sum = addTwoNumbers( 5, 7 );  // 12

add( a, b ) {
    return a + b;
}

foo = add(1, 2);            // 3
foo = add("Oh", " baby");   // "Oh baby"
```

## 实现接口

您可以使用标准的 Java 匿名内部类语法通过脚本实现接口类型。 例如：

```
ActionListener scriptedListener = new ActionListener() {
    actionPerformed( event ) { ... }
}
```

您不必编写接口的所有方法的脚本。 如果需要，您可以选择仅编写您打算调用的脚本。 如果调用代码试图调用一个未定义的方法，它只
会抛出一个异常。 如果您希望覆盖大量方法的行为 - 比如说生成一个用于日志记录的“虚拟”适配器 - 您可以在脚本对象中实现一个特
殊的方法签名：`invoke(name, args)`。 调用 `invoke()` 方法来处理任何未定义的方法调用：

```
ml = new MouseListener() {
    mousePressed( event ) { ... }
    // handle the rest
    invoke( name, args ) { print("Method: "+name+" invoked!"); }
}
```

## 捕捉异常

```
try {
    int i = 1/0;
} catch ( ArithmeticException e ) {
    print( e );
}

try {
    ...
} catch ( e ) {
    print( "caught exception: "+e );
}
```

## 简便语法

```
button = new java.awt.Button();
button.label = "my button";  // Equivalent to: b.setLabel("my button");
print( button.label );       // Equivalent to print( b.getLabel() );
```

```
Float f = new Float(42f);
print( f.infinite );  // Equivalent to print( f.isInfinite() ); // false
```

```
b = new java.awt.Button();
b{"label"} = "my button";  // Equivalent to: b.setLabel("my button");

h = new Hashtable();
h{"foo"} = "bar";          // Equivalent to: h.put("foo", "bar");
```

```
import java.util.List;
import java.util.ArrayList;

list = new ArrayList();

for(int i = 0; i < 10; i++)  {
  list.add(i);
}

for (e : list)
  print(e);
```

```
int[] array = new int[] { 1, 2, 3 };

for( i : array )
    print(i);

for( char c : "a string" )
    print( c );
```

## 导入包

在 BeanShell 中与在 Java 中一样，您可以通过类的完全限定名称来引用类，也可以从 Java 包中导入一个或多个类。

在 BeanShell 中，import 语句可以出现在任何地方，甚至是方法内部，而不仅仅是文件的顶部。 发生冲突时，较晚的导入优先于较早
的导入。

## 简便命令

```
cd("D:\\Download");
pwd();
dir("C:\\Users");
cat("C:\\Users\user.txt");
addClassPath( "D:\\mysql-connector-java-8.0.23.jar" ); // Class Loader
addClassPath( "/Users/Local/jmeter/scripts" ); // .class file Loader
source("/Users/Local/jmeter/scripts/BeanShellMethod.java"); // .java File Loader
```

## Jmeter 特殊变量

- log - 日志输出
- Label - 采样器标签名
- ctx - JMeterContext 上下文
- vars - JMeterVariables 变量线程组内有效

  ```
  vars.get("VAR1");
  vars.put("VAR2","value");
  vars.remove("VAR3");
  vars.putObject("OBJ1",new Object());
  ```

- props - JMeterProperties (class java.util.Properties) 变量全局有效

  ```
  props.get("START.HMS");
  props.put("PROP1","1234");
  ```

Last Modified 2021-05-29
