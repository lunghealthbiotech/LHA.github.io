### laravel 手动引入钉钉 Top SDK

> 1.文档下载官方sdk php包 https://open-doc.dingtalk.com/doc/sdk.htm?spm=a219a.7629140.0.0.y1jTEV&treeId=177&articleId=104963&docType=1
>
> 2.解压后改名放到laravel vendor目录下
>
> 3.编辑laravel 下的 composer.josn文件
>
> 4.在autoload files下加入
>
> ```php
>  "autoload": {
>         "classmap": [
>             "database"
>
>         ],
>         "psr-4": {
>             "App\\": "app/"
>         },
>         "files": [
>             "vendor/taobao/dingtalk/DingTalkClient.php",
>             "vendor/taobao/dingtalk/request/CorpExtListRequest.php",
>             "vendor/taobao/TopSdk.php"
>         ]
> ```
>
>  这里加载了 三个文件 前俩个为api 需要用到的类文件
>
> 第三个为钉钉SDK入口文件 
>
> 5.在sdk中 把需要用到的类 定义好命名空间 
>
> 例：
>
> ```php
> namespace Vendor\Taobao\Dingtalk;
> ```
>
> 6.在调用类中 use 该命名空间
>
> ```php
> use Vendor\Taobao\Dingtalk\DingTalkClient;
> ```
>
> 7.可以调用top api了





