title: laravel - 集合 学习记录
author: Laiyong Wang
date: 2022-01-10 14:04:44
tags:
---
#### 注意点
##### 本文档只记录我认为很有用的功能点
##### Eloquent 查询结果总是返回 Collection 实例，部分方法名一样但是功能可能不一样
#### 简介
Illuminate\Support\Collection 类提供了一个更具可读性和更便于处理数组数据的封装。具体请查看下方示例代码。 我们使用 collect 辅助函数从数组中创建一个新的集合实例，对其中每一个元素执行 strtoupper 函数之后再删除所有的空元素：
```
//这段代码首先创建了个集合，然后遍历将所有元素，将其转换为大写，然后将不为空的进行聚合
$collection = collect(['taylor', 'abigail', null])->map(function ($name) { //遍历所有元素
    return strtoupper($name); //将值进行大写转换
})->reject(function ($name) {
    return empty($name); //将不符合条件的返回
});
```
#### 创建集合
collect 辅助函数会为指定的数组返回一个新的 Illuminate\Support\Collection 实例。因此，我们可以非常容易的创建一个集合：
```
$collection = collect([1, 2, 3]);
```
#### 扩展集合
集合都是「可宏扩展」的，它允许你在执行时将其它方法添加到 Collection 类。 Illuminate\Support\Collection 类的 macro 方法接受一个闭包，该闭包将在调用你的宏时执行。 宏闭包可以通过 $this 访问集合的其他方法，就像它是集合类的真实方法一样。例如，以下代码将 toUpper 方法添加到 Collection 类中：
```
//人话就是为集合新增一个函数，供其调用，拒绝重复造轮子
use Illuminate\Support\Collection;
use Illuminate\Support\Str;

Collection::macro('toUpper', function () {
    return $this->map(function ($value) {
        return Str::upper($value);
    });
});

$collection = collect(['first', 'second']);

$upper = $collection->toUpper();

// ['FIRST', 'SECOND']
```
##### 宏参数
```
//人话就是为扩展集合的新增函数可传值，更加的自由
use Illuminate\Support\Collection;
use Illuminate\Support\Facades\Lang;
use Illuminate\Support\Str;

Collection::macro('toLocale', function ($locale) {
    return $this->map(function ($value) use ($locale) {
        return Lang::get($value, [], $locale);
    });
});

$collection = collect(['first', 'second']);

$translated = $collection->toLocale('es');
```
#### 可用方法 :https://learnku.com/docs/laravel/8.5/collections/10384#b85d9e
```
all //方法返回该集合所代表的底层数组
average //avg方法的别名
avg //返回集合中所有项目的平均值，可传参 avg(string);
chunk //分解集合，$collection->chunk(4); 四个一组
chunkWhile
collapse //将数组集合合并为单一的集合
collect
combine
concat //将给定的数组或集合的值附加到另一个集合的末尾，无视键
contains //1、传递一个闭包，来执行你的真值检验（是否全部符合）2、传递一个字符串，以确定集合是否包含给定项的值3、传递一个键 / 值对，它将检查集合中是否存在指定的键 / 值对
containsStrict //这个方法和 contains 方法类似；但是它使用了「 严格 」比照来比较所有的值 ，个人理解：加强了字段类型的验证
count //返回元素的总数
countBy //返回一个数组，键为元素值，值为元素值出现的次数
crossJoin //方法交叉连接指定数组或集合的值，返回所有可能排列的笛卡尔乘积
dd //方法用于打印集合元素并中断脚本执
diff //将集合与其它集合或者 PHP array 进行值的比较。然后返回原集合中存在而指定集合中不存在的值
diffAssoc //方法与另外一个集合或基于 PHP array 的键 / 值对进行比较。这个方法将会返回原集合不存在于指定集合的键 / 值对
diffKeys
dump //打印但不终止脚本执行
duplicates
duplicatesStrict
each //循环集合项并将其传递到回调函数中
eg：
    $collection->each(function ($item, $key) {
        //
    });
eachSpread
every //验证集合中的每一个元素是否通过指定的条件测试，contains 包含这个功能，但是集合为空是 every 返回 true ， contains 返回 false ；
except //排除指定健然后返回；与 only 相反；当使用 Eloquent 集合会修改此方法的行为
filter //使用一个回调函数过滤集合中的元素，仅保留通过回调判断为真的值，没有提供回调， 则集合中所有等于 false 的元素将会被移除 与 reject 相反
first
firstWhere
flatMap
flatten
flip //交换键与它对应的值
forget //通过指定键移除某个元素
forPage
get //返回给定键所对应的值，1、键不存在，null 将会被返回，2、第二个参数传入一个默认值，3、第二个参数传入一个回调函数做为它的默认值， 如果指定的键不存在，将会返回回调函数的结果
groupBy //通过指定的键对集合元素进行分组
has //判断给定的键是否存在与集合当中
implode //连接集合中的元素。 它的参数取决于集合元素的类型。 如果集合中包含数组或对象，您应该传入您期望连接的属性的键， 并且您期望放置在值与值之间的「glue」字符串
intersect //从给定的 array 或集合移除一些在原始集合中不存在的值 。 返回结果将会保留原始集合的键，从给定的 array 或集合移除一些在原始集合中不存在的值 。 返回结果将会保留原始集合的键
intersectByKeys
isEmpty //集合为空 返回 true ； 否则 false 会被返回
isNotEmpty //集合不为空 返回 true ； 否则 false 会被返回
join
keyBy
keys //返回集合的所有键
last 返回集合中符合条件的最后一个元素：//匿名函数function ($value, $key){ return $value > 0}
macro //允许您在运行时去增加方法到 Collection 类 ,参考扩展集合
make
map //遍历集合并将每一个值传入给定的回调函数。该回调函数可以任意修改集合项并返回，从而生成被修改过集合项的新集合
mapInto
mapSpread
mapToGroups
mapWithKeys
max //返回指定键的最大值
median //返回指定键的 中间值 
merge //将合并指定的数组或集合到原集合中。如果给定的集合项的字符串键与原集合中的字符串键相匹配，则指定集合项的值将覆盖原集合的值
mergeRecursive
min //返回指定键的最小值
mode //返回指定键的 众数值（出现次数最多的？）
nth //创建由每隔 n 个元素取一个元素组成的新集合，也可以设置指定的偏移位置作为第二个参数
only //返回集合中所有指定键的集合项
pad
partition
pipe
pipeInto
pluck //获取集合中指定键对应的所有值
pop //从原集合返回并移除集合中的最后一项
prepend //将数据值添加到集合的头部
pull //指定键对应的值从集合中移除并返回
push //把数据值追加到集合尾部
put //在集合内设置给定的键和值
random //从集合中返回一个随机值
reduce //将每次迭代的结果传递给下一次迭代，直到集合缩减为单个值
reject //使用指定的回调函数过滤集合。如果回调函数返回 true 就会把对应的元素从集合中移除
replace
replaceRecursive
reverse
search //在集合中搜索给定的值并返回它的键。如果没有找到，则返回 false
shift //移除并返回集合的第一个元素
shuffle //随机打乱元素
skip
skipUntil //将跳过元素直到给定的回调函数返回 true，然后返回集合中剩余的元素，找到条件为 true 的，然后将其及其之后的全返回
skipWhile //当回调函数返回 true 时跳过元素，然后返回集合中剩余的元素 与 reject 类似
slice //返回集合中给定索引开始后面的部分
some
sort
sortBy
sortByDesc
sortDesc
sortKeys
sortKeysDesc
splice
split
splitIn
sum  //和
take //返回给定元素数量的新集合
takeUntil //返回集合中的元素，直到给定的回调函数返回 true
takeWhile //返回集合中的元素直到给定的回调函数返回 false
tap
times
toArray //将集合转换成 PHP array。如果集合的值是 Eloquent 模型，那也会被转换成数组
toJson //将集合转换成 JSON 字符串
transform //会遍历整个集合，并对集合中的每个元素都会调用其回调函数。集合中的元素将被替换为回调函数返回的
union
unique
uniqueStrict
unless
unlessEmpty
unlessNotEmpty
unwrap
values
when //第一个参数传入为 true 时，将执行给定的回调函数
whenEmpty //第一个参数传入为 空 时，将执行给定的回调函数
whenNotEmpty //第一个参数传入不为 空 时，将执行给定的回调函数
where //通过给定的键 / 值对查询过滤集合的结果，可加第二个参数作为比较条件
whereStrict //where 的严格模式
whereBetween //筛选给定范围的集合
whereIn //根据包含给定数组的键 / 值对来过滤集合
whereInStrict //where 的严格模式
whereInstanceOf //根据给定的类来过滤集合
whereNotBetween
whereNotIn
whereNotInStrict
whereNotNull 
whereNull
wrap //将给定值封装到集合中，感觉没啥叼用
zip //在与集合的值对应的索引处合并给定数组的值
```
#### 高阶信息
集合也提供对「 高阶信息 」的支持，即集合常见操作的快捷方式。支持高阶信息的集合方法有：
average， avg， contains， each， every， filter， first， flatMap， groupBy, keyBy， map， max， min， partition， reject， skipUntil， skipWhile， some， sortBy， sortByDesc， sum， takeUntil， takeWhile， unique。

每个高阶消息都可以作为集合上的动态属性进行访问。例如，each 高阶消息在集合中的每个对象上调用一个方法:
```
//对每一条数据进行定制

use App\Models\User;

$users = User::where('votes', '>', 500)->get();

$users->each->markAsVip();
```
同样，我们可以使用 sum 高阶消息传递来收集 users 集合中的「 投票 」总数：
```
//对某一列进行定制 sum avg max min 等

$users = User::where(‘group’, ‘Development’)->get();

return $users->sum->votes;
```
参考网站 ：https://learnku.com/docs/laravel/8.5/collections/10384