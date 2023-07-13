title: PHP 对应 java aes加密
author: Laiyong Wang
date: 2023-02-23 09:38:38
tags:
---
## php7及以上
```
/**
 * @param string $string 需要加密的字符串
 * @param string $key 密钥
 * @return string
 */
function encrypt($string, $key = '9c093d47ea6feb8a5af5bd19fb758ece')
{
    $key = substr(openssl_digest(openssl_digest($key, 'sha1', true), 'sha1', true), 0, 16);
    $data = openssl_encrypt($string, 'AES-128-ECB', $key, OPENSSL_RAW_DATA);
    $data = strtoupper(bin2hex($data));
    return $data;
}

/**
 * @param string $string 需要解密的字符串
 * @param string $key 密钥
 * @return string
 */
function decrypt($string, $key = '9c093d47ea6feb8a5af5bd19fb758ece')
{
    $key = substr(openssl_digest(openssl_digest($key, 'sha1', true), 'sha1', true), 0, 16);
    $decrypted = openssl_decrypt(hex2bin($string), 'AES-128-ECB', $key, OPENSSL_RAW_DATA);
    return $decrypted;
}
```

## php7一下（我没试过）
<https://blog.csdn.net/jzm1963173402/article/details/80155493>