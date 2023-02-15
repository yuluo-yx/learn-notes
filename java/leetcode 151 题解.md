# leetcode 151 题解

- 正则表达式：替换多个为一个空格

```java
class Solution {
    public String reverseWords(String s) {
        // 去掉前后的空格
        s = s.trim();
        s = s.replaceAll("\\s+"," ");
        String[] temp = s.split(" ");
        for (int i = 0, j = temp.length - 1; i < (temp.length / 2); i ++, j --) {
            String tmp = temp[i];
            temp[i] = temp[j];
            temp[j] = tmp;
        }


        StringBuffer res = new StringBuffer();
        for (int i = 0; i < temp.length; i ++) {
            if (i == temp.length - 1) {
                res.append(temp[i]);
            } else {
                res.append(temp[i]).append(" ");
            }
        }

        return String.valueOf(res);
    }
}
```

- 正则表达式去除重复的字母

- ```
  A = A.replaceAll("(?s)(.)(?=.*\\1)", "");
  ```

