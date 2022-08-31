# KMP

功能：当出现**字符串**不匹配时，可以记录一部分之前已经匹配好的文本内容，利用这些信息避免从头再去匹配。

```c++
class Solution {
public:
    void arrayNext(int *next, const string& s) {
        int j = 0;
        next[j] = 0;
          for (int i = 1; i < s.size(); i++) {
            while (s[i] != s[j] && j > 0) {
                j = next[j - 1];
            }
            if (s[i] == s[j])
                j++;
            next[i] = j;
        }
    }
    bool repeatedSubstringPattern(string s) {
        if (s.size() == 0)
            return false;
        int next[s.size()];
        arrayNext(next, s);
        if (next[s.size() - 1] != 0 && s.size() % (s.size() - next[s.size() - 1]) == 0)
            return true;
        return false;
    }
};
```

#### 如何记录已经匹配的文本内容，是KMP的重点——>next数组

next数组就是一个前缀表，前缀表是用来回退的，它记录了模式串与主串(文本串)不匹配的时候，模式串应该从哪里开始重新匹配。

例子：要在文本串：aabaabaafa 中查找是否出现过一个模式串：aabaaf。

![KMP详解1](https://code-thinking.cdn.bcebos.com/gifs/KMP%E7%B2%BE%E8%AE%B21.gif)

前缀表：记录下标i之前（包括i）的字符串中，有多大长度的相同前缀后缀。

#### 最长公共前后缀

前缀是指不包含最后一个字符的所有以第一个字符开头的连续子串。

后缀是指不包含第一个字符的所有以最后一个字符结尾的连续子串。

因为前缀表要求的就是相同前后缀的长度。

![KMP精讲2](https://code-thinking.cdn.bcebos.com/pics/KMP%E7%B2%BE%E8%AE%B22.png)

5之前这部分的字符串（也就是字符串aabaa）的最长相等的前缀 和 后缀字符串是 子字符串aa ，因为找到了最长相等的前缀和后缀，匹配失败的位置是后缀子串的后面，那么我们找到与其相同的前缀的后面从新匹配就可以了。

#### 计算前缀表

| 下标   | 0    | 1    | 2    | 3    | 4    | 5    |
| ------ | ---- | ---- | ---- | ---- | ---- | ---- |
| 模式串 | a    | a    | b    | a    | a    | f    |
| 前缀表 | 0    | 1    | 0    | 1    | 2    | 0    |

长度为前1个字符的子串`a`，最长相同前后缀的长度为0（前缀为0（不包含第一个字符的），后缀为0）

长度为前2个字符的子串`aa`，最长相同前后缀的长度为1（前缀为2，后缀为0）

长度为前1个字符的子串`aab`，最长相同前后缀的长度为0（前缀为2，后缀为0）

后面依次类推

| 下标   | 0    | 1    | 2    | 3    | 4    | 5    |
| ------ | ---- | ---- | ---- | ---- | ---- | ---- |
| 模式串 | a    | a    | b    | a    | a    | b    |
| 前缀表 | 0    | 1    | 0    | 1    | 2    | 3    |

前缀表的数值就是next数组

```c++
next[s.size()] = {0, 1, 0, 1, 2, 3};
```

力扣题 28、459
