title: phpWrite
author: Laiyong Wang
date: 2022-08-27 13:48:55
tags:
---
面试还有笔试题，真的烦人，关键还全是英文
我现在也整理下 PHP 笔试题
1. 一下代码输出的是什么
```
//(1)
$b = 100;
$c = 50;
$a = $b>$c?100:50;
echo $a;	//100

//(2)
$str = “php”;
$$str = “teems”;
$$str .= “coffice”;
echo $php;	//teemscoffice

//(3)
$a = “b”;
$b = “a”;
$ar[$a] = $b;
$ar[$b] = $a;
echo $ar[‘a’];	//b

//(4)
$n = 3.5;
$a = floor($n);
$b = round($n);
echo $a.$b;		//34
echo $a+$b;		//7
//注释：floor()：向下取整
//round() 4舍5入取整
//$a=3;$b=4;

```
2.  写个方法，遍历所有文件名
```
//5
listDir($dir);

function listDir($dir="./"){

    if (is_dir($dir)){
        $handle = opendir($dir);
        while (($file = readdir($handle)) !== false){
            if ($file == "." || $file == ".."){
                continue;
            }
            if (is_dir($sub_dir = realpath($dir.'/'.$file))){
                echo "文件夹下的文件：".$dir."：".$file.PHP_EOL;
                listDir($sub_dir);
            }else{
                echo "文件名为：".$file.PHP_EOL;
            }
        }
        closedir($handle);

    }else{
        echo $dir."不是目录";
    }

}
```
3. 从一个标准URL中取出文件的扩展名
```
$arr = parse_url('http://www.sina.com.cn/abc/de/fg.php?id=1');
$result = pathinfo($arr['path']);
var_dump($result['extension']);
```
4. 