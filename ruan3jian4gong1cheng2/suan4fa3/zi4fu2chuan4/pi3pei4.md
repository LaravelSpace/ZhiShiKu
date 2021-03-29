## 字符串匹配算法

最直接的办法叫BF(Brute-Force)算法，中文叫作暴力匹配算法，也叫朴素匹配算法

字符串匹配算发改进的基本思路都是减少匹配的次数

### RK(Rabin-Karp)算法

先计算模式串的哈希值，在进行匹配的过程中，先计算匹配串的哈希值，如果哈希值算出来一样，就进行一次暴力匹配

### BM(Boyer-Moore)算法

算法分为两部分，坏字符规则和好后缀规则，两者都会得到一个滑动距离，选大的

与别的算法不同的是，BM算法是从后往前进行比较的

##### 坏字符规则

```
abcacabdc -> abcacabdc
abd       ->    abd
```

匹配串和模式串在匹配串`c`处失配，称`c`为坏字符

如果坏字符在模式串中不存在，就可以直接把模式串向后滑动一个模式串长度

```
dcacdcabdc -> dcacdcabdc
cbcd       ->  cbcd
```

如果坏字符在模式串中存在，就把模式串中靠后位置的对应字符和坏字符对齐

```
aaaaaaa ->    aaaaaaa
baaa    -> baaa
```

```
dcacdcabdc -> dcacdcabdc
cbcd       ->    cbcd
```

但是单纯使用坏字符规则会出现向前滑动或者过度滑动的问题

##### 好后缀规则

好后缀规则又分为两部分，好后缀和模式串中间的关系，好后缀和模式串前缀的关系

模式串后缀和模式串中间的关系

```
dsaabcbcbcb
adcbbc
```

匹配串和模式串在匹配串`a`处失配，但是失配之前匹配了`bc`，称`bc`为好后缀

在模式串前面寻找好后缀，如果找不到就把模式串向后滑动一个模式串长度

```
dsaabcbcbcb -> dsaabcbcbcb
abcbbc      ->    abcbbc
```

如果能找到对应的字符组合，就把模式串中靠后位置的字符组合和好后缀对齐

```
dsaabcabccb -> dsaabcabccb
  cabc      ->       cabc
```

但是单纯的判断好后缀和模式串中间的关系会出现过度滑动的问题

这里就需要再考虑模式串后缀和模式串前缀的关系

```
dsaabcabccb -> dsaabcabccb
  cabc      ->      cabc
```

如果好后缀和模式串中间不存在匹配，那么有两种情况，一种是上面的完全不匹配，另外一种就是这里需要考虑的，好后缀的一部分和模式串前缀能匹配的情况，比如这里的好后缀`abc`和模式串前面的`c`可以匹配

### KMP(Knuth-Morris-Pratt)算法



---

| 代码说明 | 代码位置                                           |
| -------- | -------------------------------------------------- |
| BM算法   | CYangLi/suan4fa3/zi4fu2chuan4/boyer_moore.c        |
| RK算法   | CYangLi/suan4fa3/zi4fu2chuan4/rabin_karp.c         |
| KMP算法  | CYangLi/suan4fa3/zi4fu2chuan4/knuth_morris_pratt.c |