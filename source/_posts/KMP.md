title: 字符串匹配算法 BF 、KMP等
author: Laiyong Wang
tags:
  - 算法
categories: []
date: 2022-11-10 09:23:00
---
## 本文我将全用 php 语言来进行实现
### 参考链接 : <https://www.cnblogs.com/zhangtianq/p/5839909.html>
#### BF算法（串模式匹配算法）

```
//i、j一起移动
function violentMatch(string $s, string $p){
    $sLen = strlen($s);
    $pLen = strlen($p);
    $i = 0;
    $j = 0;
    while($i<$sLen && $j<$pLen){
        if ($s[$i] == $p[$j]){
            $i++;
            $j++;
        }else{
            $i = $i-$j+1;
            $j = 0;
        }
    }
    if($j == $pLen){
        return $i -$j +1;
    } else {
        return -1;
    }
} 
```
#### KMP算法
```
//主逻辑，重点在于next的求法，看 nextArr 方法
//i不动j动
function kmpSearch(string $s, string $p)
{
    $sLen = strlen($s);
    $pLen = strlen($p);
    $i = 0;
    $j = 0;
    $next = nextArr($p);
    while ($i < $sLen && $j < $pLen) {
        if ($j == -1 || $s[$i] == $p[$j]) {
            $i++;
            $j++;
        }else{
            $j=$next[$j];
        }
    }
    if ($j == $pLen){
        return $i - $j;
    }
    return  -1;
}

//返回 next 数组，即是每个子串的最大公共长度数组右移一位，然后填充 -1
//也可以理解成这个字符之前的字符串中有多大长度的相同前缀后缀
function nextArr(string $p): array
{
    $res = [];
    $res[0] = -1;
    $pLen = strlen($p);
    for ($i = 1; $i < $pLen; $i++) {
        $res[$i] = maxLen(mb_substr($p, 0, $i));
    }
    return $res;
}
//返回字符串前后缀公共长度
function maxLen($str): int
{
    $maxLen = 0;
    $sLen = strlen($str);
    if ($sLen != 1) {
        for ($i = 2; $i <= $sLen; $i++) {
            if (mb_substr($str, 0, $i - 1) == mb_substr($str, $sLen - $i + 1, $i - 1)) {
                $maxLen = $i - 1;
            }
        }
    }
    return $maxLen;
}

echo kmpSearch('bbc abcdab abcdabcdabde','abcdabd');
```