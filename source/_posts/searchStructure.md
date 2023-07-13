title: 数据表结构的查找算法
author: Laiyong Wang
date: 2022-11-15 13:19:28
tags:
---
### 数据表
同一类型的数据元素构成的集合
#### 顺序查找
```
//循环即可，插入时添加至最后就行
foreach($arr as $key=>$val){
	if($val == $query){
   	return $key; 
   }
}
```
#### 二分查找（折半查找）算法
```
//在有序的一串数字对某数字进行查找
function binarySearch($arr,$query){
    if (empty($arr))return -1;
    $total = count($arr);
    if ($total == 1){
        if($arr[0] == $query){
            return true;
        } else {
            return false;
        }
    }
    switch ($arr[ceil($total/2)] <=> $query){
        case 1:
            return binarySearch(array_slice($arr,0,floor($total/2)),$query);
        case 0:
            return true;
        case -1:
            return binarySearch(array_slice($arr,floor($total/2)),$query);
    }
}
$res =  binarySearch([5,13,19,21,37,56,64,75,80,88,92],13);
```

#### 分块查找（索引顺序查找）算法
类似于数据库的索引表和数据表
索引表有顺序，用二分查找确定数据在那个数据块，在顺序查找数据块判断数据是否在里面

#### 静态树表查找算法
感觉没啥吊用，不总结了

#### 二叉排序树（二叉查找树）
二叉排序树要么是空二叉树，要么具有如下特点：
  二叉排序树中，如果其根结点有左子树，那么左子树上所有结点的值都小于根结点的值
  二叉排序树中，如果其根结点有右子树，那么右子树上所有结点的值都大小根结点的值
  二叉排序树的左右子树也要求都是二叉排序树
还行吧，也感觉没啥吊用，无限递归可实现，懒得实现
#### 平衡二叉树（AVL树）
两个特点的二叉树：
  每棵子树中的左子树和右子树的深度差不能超过 1
  二叉树中每棵子树都要求是平衡二叉树
我也不知道有啥吊用，难点在于二叉排序树转换成平衡二叉树
#### 红黑树（一种比较高效的二叉查找树）

#### B-树及其基本操作

#### B+树及其基本操作

#### 键树查找法

#### 哈希表（散列表）

#### 哈希查找算法