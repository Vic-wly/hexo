title: php 生成 zip 压缩文件
author: Laiyong Wang
tags:
  - 压缩
  - ''
categories: []
date: 2023-02-21 17:06:00
---
```
    function addFileToZip($path, $zip, $dir)
    {
        $handler = opendir($path); //打开当前文件夹由$path指定。
        while (($filename = readdir($handler)) !== false) {
            if ($filename != "." && $filename != "..") {//文件夹文件名字为'.'和‘..’，不要对他们进行操作
                if (is_dir($path . "/" . $filename)) {// 如果读取的某个对象是文件夹，则递归
                    $this->addFileToZip($path . "/" . $filename, $zip, $dir);
                } else { //将文件加入zip对象
                    $file = file_get_contents($path . "/" . $filename);
                    $filename = str_replace($dir . '/', "", $path . "/" . $filename);//将多余的绝对路径替换，使生成的zip包仅有需要压缩的文件，就算方法的第三个参数，替换成空
                    $zip->addFromString($filename, $file);
                }
            }
        }
        @closedir($path);
    }

    $zip = new ZipArchive();
    $zip->open(Yii::$app->params['saveDir'] . DIRECTORY_SEPARATOR . $paperInfo->guid . '.zip', ZipArchive::CREATE);
    addFileToZip($saveDir, $zip, $saveDir); //调用方法，对要打包的根目录进行操作，并将ZipArchive的对象传递给方法
    $zip->close();

```