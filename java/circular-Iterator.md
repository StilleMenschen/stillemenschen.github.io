# 循环迭代器

```java
package tech.tystnad.infrastructure.utility;

import java.util.Iterator;
import java.util.Objects;

/**
 * 可以无限循环的迭代器
 */
public class CircularIterator<E> implements Iterator<E> {

    private final E[] array; // 输入的数组
    private final int length;
    private int index; // 当前元素的索引

    public CircularIterator(E[] array) {
        this.array = Objects.requireNonNull(array);
        this.index = 0;
        this.length = array.length;
    }

    @Override
    public boolean hasNext() {
        return length > 0; // 只要数组不为空且长度大于0，就有下一个元素
    }

    @Override
    public E next() {
        E element = array[index]; // 获取当前元素
        index = (index + 1) % length; // 将索引加一并取模，实现循环
        return element;
    }

    @Override
    public void remove() {
        // 这里可以根据需要实现删除逻辑，比如使用System.arraycopy()方法移动数组元素
        throw new UnsupportedOperationException("Not implemented");
    }
}
```

Last Modified 2023-03-18
