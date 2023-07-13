title: soap 简单使用
author: Laiyong Wang
date: 2023-02-23 09:30:30
tags:
---
```
/**
 * @param $url 请求wsdl地址
 * @param $method 请求方法
 * @param $content 请求参数
 * @return array
*/
function request($url, $method, $content)
{
    $params = array('location' => $url);//为了目标网址为 https 也能通
    try {
        $soap = new SoapClient($url, $params);//实例化
    } catch (\SoapFault $e) {
        return [
            'status' => 1,
            'msg' => $e->getMessage(),
            'data' => [],
        ];
    }

    try {
        $res = $soap->__soapCall($method, $content);//请求
    } catch (\SoapFault $e) {
        return [
            'status' => 1,
            'msg' => $e->getMessage(),
            'data' => '',
        ];
    }

    return json_decode($res, true);
}


```