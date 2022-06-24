title: laravel - 使用工厂、seeder 、fake等模块进行数据填充
author: Laiyong Wang
date: 2022-01-15 10:08:39
tags:
---
#### 注意点
本文档写的较为简单
只填充单表数据
#### 工厂
##### 创建工厂
```
php artisan make:factory FlightFactory
//或者
php artisan make:factory PostFactory --model=Post //指定 model
```
##### 写工厂
注意引入需要使用的类
```
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Flight;

class FlightFactory extends Factory
{
    protected $model = Flight::class;
    /**
     * Define the model's default state.
     *
     * @return array
     */
    public function definition()
    {
    	// return 里的字段需要与表中一致，少点没事
        return [
            'name' => $this->faker->name,
            'email' => $this->faker->unique()->safeEmail,
            'age' => $this->faker->randomDigit,
        ];
    }
}

```
#### seeder
##### 创建 seeder 文件
```
php artisan make:seeder FlightTableSeeder
```
##### 写 seeder 文件
注意引入需要使用的类
```
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Flight;

class FlightTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        // 生成数据集合
        Flight::factory()->count(10)->create();
        //可定制数据
        $flight = Flight::find(1);
        $flight->name = 'Summer';
        $flight->email = 'summer@example.com';
        $flight->save();
    }
}
```
##### 增加配置
在 DatabaseSeeder 中调用 call 方法来指定我们要运行假数据填充的文件
database/seeders/DatabaseSeeder.php
```
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run()
    {
        $this->call(FlightTableSeeder::class); //新增一行
    }
}

```
#### 开始填充数据
```
php artisan migrate:refresh //不推荐执行，清洗数据
php artisan db:seed //不推荐执行，所有填充文件都执行，看自己需求
php artisan db:seed --class=FlightTableSeeder //推荐执行，只执行对应的，精准填充数据，
```