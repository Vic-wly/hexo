title: leetCode
author: Laiyong Wang
date: 2022-11-07 13:23:46
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
        $arr = [];
        foreach($nums as $key => $val){
            $arr[$val] += 1;
        }
        return array_search(1,$arr);

    }
}
```
2. 给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素
```
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
2. 编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target 。该矩阵具有以下特性：每行的元素从左到右升序排列,每列的元素从上到下升序排。
```
class Solution {

    /**
     * @param Integer[][] $matrix
     * @param Integer $target
     * @return Boolean
     */
    function searchMatrix($matrix, $target) {
        $total = count($matrix) - 1;//行
        $line = 0;
        while(true){
            if (!isset($matrix[$total][$line])) return false;
            if ($matrix[$total][$line] == $target){
                return true;
            }elseif($matrix[$total][$line] < $target){
                $line++;
            } else {
                $total--;
            }
        }
        return false;
    }
}
```