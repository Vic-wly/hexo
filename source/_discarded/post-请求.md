title: post 请求
author: Laiyong Wang
date: 2023-02-23 10:27:35
tags:
---
```
function postCurl($url, $data, $header = [])
{
    if (empty($header)) {
        $header = ['Content-Type: application/json; charset=utf-8'];
    }
    $ch = curl_init();//初始化
    curl_setopt($ch, CURLOPT_URL, $url);//抓取指定网页 curl_setopt就算设置变量用变
    curl_setopt($ch, CURLOPT_POST, 1);//设置为POST方式
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0); // 对认证证书来源的检查
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0); // 从证书中检查SSL加密算法是否存在
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data, JSON_UNESCAPED_UNICODE));//POST数据
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1); //如果成功只将结果返回，不自动输出任何内容。
    curl_setopt($ch, CURLOPT_HEADER, 0);//如果想把一个头包含在输出中，设置这个选项为一个非零值。
    curl_setopt($ch, CURLOPT_HTTPHEADER, $header);
    curl_setopt($ch, CURLINFO_HEADER_OUT, 0);//启用时追踪句柄的请求字符串。
    $result = curl_exec($ch);//执行并获取结果
    curl_close($ch);//释放句柄

    return json_decode($result, true);
}
```