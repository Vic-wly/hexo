title: LeetCode 简单算法 - 自己实现
author: Laiyong Wang
date: 2022-07-28 08:37:49
tags:
---
1. 给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素
```
class Solution {

    /**
     * @param Integer[] $nums
     * @return Integer
     */
    function singleNumber($nums) {
        foreach($nums as $key => $val){
            $bool = true;
            foreach($nums as $k => $v){
                if($key != $k && $val == $v){
                    $bool = false;
                }
            }
            if($bool){
                return $val;
            }
        }

    }
}
```
2. 给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素
```
//
class Solution {

    /**
     * @param Integer[] $nums
     * @return Integer
     */
    function majorityElement($nums) {
        $length = count($nums);
        $result = [];
        foreach($nums as $val){
            if(array_key_exists($val,$result)){
                $result[$val]++;
            } else {
                $result[$val] = 1;
            }
        }
        foreach($result as $k=>$v){
            if($v > $length/2){
                return $k;
            }
        }
    }
}
```