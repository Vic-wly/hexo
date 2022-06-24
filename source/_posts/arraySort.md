title: 二维数组以某一列的大小正向排序
author: Laiyong Wang
date: 2022-06-20 15:55:25
tags:
---
##### 二维数组以某一列的大小正向排序
```
//代码示例
$factoryWave = [
    [
        'qwe' => 3,
        'weight_kg' => 3,
    ],
    [
        'qwe' => 4,
        'weight_kg' => 4
    ],
    [
        'qwe' => 1,
        'weight_kg' => 1,
    ],
    [
        'qwe' => 2,
        'weight_kg' => 2,
    ],
];

$weightKgArr = array_column($factoryWave, 'weight_kg');
array_multisort($weightKgArr, $factoryWave);
var_dump($weightKgArr);
var_dump($factoryWave);
```