title: laravel - 查询构造器学习记录
author: Laiyong Wang
date: 2022-01-11 14:23:28
tags:
---
#### 注意点
##### 本文档只记录我认为很有用的功能点
##### 参考网站：https://learnku.com/docs/laravel/8.5/queries/10404
#### 基础的 where 语句
##### where 语句
where 方法需要三个基本参数。第一个参数是字段的名称。第二个参数是一个操作符，它可以是数据库中支持的任意操作符。第三个参数是与字段比较的值。

例如。在 users 表中查询 votes 字段等于 100 并且 age 字段大于 35 的数据：
```
$users = DB::table('users')
                ->where('votes', '=', 100)
                ->where('age', '>', 35)
                ->get();
```
方便起见。如果你想要比较一个字段的值是否等于给定的值。你可以将这个给定的值作为第二个参数传递给 where 方法。那么，Laravel 会默认使用 = 操作符
```
$users = DB::table('users')->where('votes', 100)->get();
```
如上所述，您可以使用数据库支持的任意操作符：
```
$users = DB::table('users')
                ->where('votes', '>=', 100)
                ->get();

$users = DB::table('users')
                ->where('votes', '<>', 100)
                ->get();

$users = DB::table('users')
                ->where('name', 'like', 'T%')
                ->get();
```
也可以将一个条件数组传递给 where 方法。通常传递给 where 方法的数组中的每一个元素都应该包含 3 个元素：
```
$users = DB::table('users')->where([
    ['status', '=', '1'],
    ['subscribed', '<>', '1'],
])->get();
```
##### orWhere 语句
当链式调用多个 where 方法的时候，这些 where 语句将会被看成是 and 关系。另外，您也可以在查询语句中使用 orWhere 方法来表示 or 关系。orWhere 方法接收的参数和 where 方法接收的参数一样：
```
$users = DB::table('users')
                    ->where('votes', '>', 100)
                    ->orWhere('name', 'John')
                    ->get();
```
如果您需要在括号内对 or 条件进行分组，那么可以传递一个闭包作为 orWhere 方法的第一个参数：
```
$users = DB::table('users')
            ->where('votes', '>', 100)
            ->orWhere(function($query) {
                $query->where('name', 'Abigail')
                      ->where('votes', '>', 50);
            })
            ->get();
```
上面的例子将会生成下面的 SQL：
```
select * from users where votes > 100 or (name = 'Abigail' and votes > 50)
```
```
```
```
```
```
```
```
```
#### 子连接查询
你可以使用 joinSub，leftJoinSub 和 rightJoinSub 方法关联一个查询作为子查询。他们每一种方法都会接收三个参数：子查询，表别名和定义关联字段的闭包。
如下面这个例子，获取发过博客的的用户集合：
```
$latestPosts = DB::table('posts')
                   ->select('user_id', DB::raw('MAX(created_at) as last_post_created_at'))
                   ->where('is_published', true)
                   ->groupBy('user_id');

$users = DB::table('users')
        ->joinSub($latestPosts, 'latest_posts', function ($join) {
            $join->on('users.id', '=', 'latest_posts.user_id');
        })->get();
```