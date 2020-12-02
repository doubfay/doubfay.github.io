[剑指 Offer 05. 替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)
> 请实现一个函数，把字符串 s 中的每个空格替换成"%20"。
  
1. 直接使用 Java String 的库函数

```java
class Solution {
    public String replaceSpace(String s) {
        return s.replace(" ", "%20");       
    }
}
```

库函数的底层实现，用的是 `StringBuilder`

```java
final class String {
    /**
     * Replaces each substring of this string that matches the literal target
     * sequence with the specified literal replacement sequence. The
     * replacement proceeds from the beginning of the string to the end, for
     * example, replacing "aa" with "b" in the string "aaa" will result in
     * "ba" rather than "ab".
     *
     * @param  target The sequence of char values to be replaced
     * @param  replacement The replacement sequence of char values
     * @return  The resulting string
     * @since 1.5
     */
    public String replace(CharSequence target, CharSequence replacement) {
        String tgtStr = target.toString();
        String replStr = replacement.toString();
        int j = indexOf(tgtStr);
        if (j < 0) {
            return this;
        }
        int tgtLen = tgtStr.length();
        int tgtLen1 = Math.max(tgtLen, 1);
        int thisLen = length();

        int newLenHint = thisLen - tgtLen + replStr.length();
        if (newLenHint < 0) {
            throw new OutOfMemoryError();
        }
        StringBuilder sb = new StringBuilder(newLenHint);
        int i = 0;
        do {
            sb.append(this, i, j).append(replStr);
            i = j + tgtLen;
        } while (j < thisLen && (j = indexOf(tgtStr, j + tgtLen1)) > 0);
        return sb.append(this, i, thisLen).toString();
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(n)

2. 用 `StringBuilder` 实现

既然底层还是用 `StringBuilder`，不妨我们自己直接实现

```java
class Solution {
    public String replaceSpace(String s) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (c == ' ') {
                sb.append("%20");
            } else {
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```

有个细节，在新建一个 `StringBuilder` 对象时，`new StringBuilder()` 默认创建的是一个 16 位长度的字符数组 `new char[16]`    
如果在 `append` 的时候，超出了 16 位的初始容量大小，就会触发扩容 `ensureCapacityInternal(count + len);`  
扩容时，会进行字符数组的拷贝，如果字符串 `s` 比较长，会进行频繁的数组拷贝

```java
abstract class AbstractStringBuilder {
    /**
     * For positive values of {@code minimumCapacity}, this method
     * behaves like {@code ensureCapacity}, however it is never
     * synchronized.
     * If {@code minimumCapacity} is non positive due to numeric
     * overflow, this method throws {@code OutOfMemoryError}.
     */
    private void ensureCapacityInternal(int minimumCapacity) {
        // overflow-conscious code
        if (minimumCapacity - value.length > 0) {
            value = Arrays.copyOf(value,
                    newCapacity(minimumCapacity));
        }
    }
}
```

优化点：可以在创建 `StringBuilder` 对象时，指定长度为 `s.length() * 3`，因为从 " " 变到 "%20"，最坏情况下，长度是原来的 3 倍

`StringBuilder sb = new StringBuilder(s.length() * 3);`

* 时间复杂度：O(n)
* 空间复杂度：O(n)

3. 用字符数组实现

从上面我们知道了 `StringBuilder` 底层用的其实是字符数组，那我们不妨直接用字符数组实现替换

```java
class Solution {
    public String replaceSpace(String s) {
        char[] newSChars = new char[s.length() * 3];
        int length = 0;
        for (char c : s.toCharArray()) {
            if (c == ' ') {
                newSChars[length++] = '%';
                newSChars[length++] = '2';
                newSChars[length++] = '0';
            } else {
                newSChars[length++] = c;
            }
        }
        return new String(newSChars, 0, length);
    }
}
```

虽然写法上有所不同，但是本质上来看，方法 3 和方法 2 是一样的，方法 3 只是将 `StringBuilder` 外面的一层封装剥离开了，展示了更底层的实现方式

* 时间复杂度：O(n)
* 空间复杂度：O(n)
