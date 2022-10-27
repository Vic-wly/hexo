title: 'PHP ReflectionClass::getProperties 反射函数'
author: Laiyong Wang
date: 2022-10-08 14:13:36
tags:
---
ReflectionProperty::IS_STATIC - 指示了 static 的属性。
ReflectionProperty::IS_PUBLIC - 指示了 public 的属性。
ReflectionProperty::IS_PROTECTED - 指示了 protected 的属性。
ReflectionProperty::IS_PRIVATE - 指示了 private 的属性。

<b>ReflectionProperty::IS_PUBLIC 包含 ReflectionProperty::IS_STATIC

不知道是不是 bug 还是 static 就是公共的，的确可以随便访问
</b>
##### 可以这样处理
```
$class = new ReflectionClass($this);
$names = [];
foreach ($class->getProperties(\ReflectionProperty::IS_PUBLIC) as $property) {
    if (!$property->isStatic()) {
        $names[] = $property->getName();
    }
}
```
        