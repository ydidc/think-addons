# think-addons
The ThinkPHP 6 Addons Package

## 安装
> composer require ydidc/think-addons


## 创建插件
> 创建的插件可以在view视图中使用，也可以在php业务中使用
 
安装完成后访问系统时会在项目根目录生成名为`addons`的目录，在该目录中创建需要的插件。

下面写一个例子：

### 创建demo插件
> 在addons目录中创建demo目录

### 创建钩子实现类
> 在demo目录中创建同名 Demo.php 类文件。注意：类文件首字母需大写

```php
<?php
namespace addons\demo;	// 注意命名空间规范

use think\Addons;

/**
 * 插件测试
 */
class Demo extends Addons			// 继承think\Addons类
{
    // 该插件的基础信息
    public $info = [
        'name' => 'demo',			// 插件标识
        'title' => '示例插件',			// 插件名称
        'description' => 'thinkph6示例demo插件',	// 插件简介
        'author' => 'demo',
        'version' => '1.0.0'
    ];

    /**
     * 插件安装方法
     * @return bool
     */
    public function install()
    {
        return true;
    }

    /**
     * 插件卸载方法
     * @return bool
     */
    public function uninstall()
    {
        return true;
    }

    /**
     * 实现的testHook钩子方法 // 后缀必须 Hook
     * @return mixed
     */
    public function demoHook($param)
    {
	// 调用钩子时候的参数信息
        print_r($param);
	// 当前插件的配置信息，配置信息存在当前目录的config.php文件中，见下方
        print_r($this->getConfig());
	// 可以返回模板，模板文件默认读取的为插件目录中的文件。模板名不能为空！
        return $this->fetch('info');
    }

}
```

### 创建插件配置文件
> 在test目录中创建config.php类文件，插件配置文件可以省略。

```php
<?php
return [
    "base" => [
        "title"=>"基本模块",
        "item"=>[
            [
                "name"=>"title",
                "title"=>"标题",
                "type"=>"text",
                "tips"=>"提示文字信息",
                "value"=>"默认值",
                "status"=>1
            ],
	    [
                "name"=>"image",
                "title"=>"演示效果",
                "type"=>"image",
                "tips"=>"请上传图片",
                "value"=>"",
                "status"=>1
            ]
	]
    ]
];
```

### 创建钩子模板文件
> 在demo->view目录中创建info.html模板文件，钩子在使用fetch方法时对应的模板文件。

```html
<h1>hello tpl</h1>

如果插件中需要有链接或提交数据的业务，可以在插件中创建controller业务文件，
要访问插件中的controller时使用addon_url生成url链接。
如下：
<a href="{:addons_url('Action/link')}">link test</a>
或
<a href="{:addons_url('demo://Action/link')}">link test</a>
格式为：
test为插件名，Action为controller中的类名[多级控制器可以用.分割]，link为controller中的方法
```

### 创建插件的controller文件
> 在test目录中创建controller目录，在controller目录中创建Index.php文件
> controller类的用法与tp6中的controller一致

```php
<?php
namespace addons\demo\controller;

//可继承前台类，提供 $this->success(); $this->error(); 方法，可自己扩展。
use think\Addons\HomeController;

class Index extends HomeController
{
    public function link()
    {
        echo 'hello link';
    }
}
```

## 使用钩子
> 创建好插件后就可以在正常业务中使用该插件中的钩子了
> 使用钩子的时候第二个参数可以省略

### 模板中使用钩子

```html
<div>{:hook('demoHook', ['id'=>1])}</div>
```

### php业务中使用
> 只要是thinkphp6正常流程中的任意位置均可以使用

```php
hook('demoHook', ['id'=>1])
```

### 插件公共方法
```php
/**
 * 处理插件钩子
 * @param string $event 钩子名称
 * @param array|null $params 传入参数
 * @param bool $once 是否只返回一个结果
 * @return mixed
 */
function hook($event, $params = null, bool $once = false);

/**
 * 读取插件的基础信息
 * @param string $name 插件名
 * @return array
 */
function get_addons_info($name);

/**
 * 获取插件Plugin的单例
 * @param string $name 插件名
 * @return mixed|null
 */
function get_addons_instance($name);

/**
 * 插件显示内容里生成访问插件的url
 * @param $url 在插件控制器中可忽略插件名，在非插件中生成时需指定插件名。例：插件名://控制器/方法
 * @param array $param
 * @param bool|string $suffix 生成的URL后缀
 * @param bool|string $domain 域名
 * @return bool|string
 */
function addons_url($url = '', $param = [], $suffix = true, $domain = false);

```
