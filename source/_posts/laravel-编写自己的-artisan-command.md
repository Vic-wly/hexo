title: laravel - 编写自己的 artisan command
author: Laiyong Wang
date: 2022-01-17 16:57:02
tags:
---
参考网址：https://laravelacademy.org/post/21998

#### 生成命令：
```
php artisan make:command nimaCmd //正常的项目
php artisan module:make-command Maxwell/nimaCmd Test //module 模块化
```

#### 命令结构
命令生成以后，需要填写该类的 signature 和 description 属性， 这两个属性在调用 list 显示命令的时候会被展示。handle 方法在命令执行时被调用，你可以将所有命令逻辑都放在这个方法里面。
我写的例子中使用 name 替换了 signature，替换的好处是 不用写繁琐的 参数拼接，eg：
```
//原写法
protected $signature = 'email:send {user}';
//使用 name 的命令行使用方式
php artisan  wanglaiyong doushi  dashuaibi -Q asd -A qwe
```

```
<?php

namespace Modules\Test\Console\Maxwell;

use Illuminate\Console\Command;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Input\InputArgument;

class nimaCmd extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $name = 'wanglaiyong';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = '我是大帅比';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $example = $this->argument('example'); //获取参数
        $zxc = $this->argument('zxc'); //获取参数
        $asd = $this->option('asd'); //获取选项
        $qwe = $this->option('qwe'); //获取选项
        $this->info($example . '--' . $zxc . '--' .$asd . '--' .$qwe); //小黑窗输出信息
    }

    /**
     * Get the console command arguments.
     *
     * @return array
     */
    protected function getArguments()
    {
    	//定义参数
        return [
            ['example', InputArgument::REQUIRED, 'An example argument.'],
            ['zxc', InputArgument::REQUIRED, 'An example argument.'],
        ];
    }

    /**
     * Get the console command options.
     *
     * @return array
     */
    protected function getOptions()
    {
    	//定义选项
        return [
            ['asd', '-A', InputOption::VALUE_OPTIONAL, 'An example option.', null],
            ['qwe', '-Q', InputOption::VALUE_OPTIONAL, 'An example option.', null],
        ];
    }
}

```