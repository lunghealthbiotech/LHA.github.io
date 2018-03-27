### laravel 手动创建分页构造器

> laravel自带的paginate方法可以很方便的搞定分页功能，但是有些时候，数据不是在数据库查出来的，需要自定义分页。

##### 假设$arr为要分页的数据

```php
$arr =[
  {
    "id": 1,
    "name": 'zhangsan',
    "org_id": 2,
    "user_id": 8,
    "papaer_id": 26,
  },
  {
    "id": 2,
    "name": 'lisi',
    "org_id": 2,
    "user_id": 8,
    "papaer_id": 26,
  },
  {
    "id": 3,
    "name": 'wanger',
    "org_id": 2,
    "user_id": 8,
    "papaer_id": 26,
  }
];


```

##### laravel paginate方法 源码

> path:#vendor/laravel/framework/src/Illuminate/Database/Eloquent/Builder.php:480
>
> ```php
> public function paginate($perPage = null, $columns = ['*'], $pageName = 'page', $page = null)
> {
>         $query = $this->toBase();
>
>         $total = $query->getCountForPagination();
>
>         $this->forPage(
>             $page = $page ?: Paginator::resolveCurrentPage($pageName),
>             $perPage = $perPage ?: $this->model->getPerPage()
>         );
> 		//最终实现方法是实例化了 LengthAwarePaginator类的构造方法
>         return new LengthAwarePaginator($this->get($columns), $total, $perPage, $page, [
>             'path' => Paginator::resolveCurrentPath(),
>             'pageName' => $pageName,
>         ]);
> }
> ```
>
> ```php
> public function __construct($items, $total, $perPage, $currentPage = null, array $options = [])
> {
>        　foreach ($options as $key => $value) {
>             $this->{$key} = $value;
>         }
>
>         $this->total = $total;
>         $this->perPage = $perPage;
>         $this->lastPage = (int) ceil($total / $perPage);
>         $this->path = $this->path != '/' ? rtrim($this->path, '/') : $this->path;
>         $this->currentPage = $this->setCurrentPage($currentPage, $this->lastPage);
>         $this->items = $items instanceof Collection ? $items : Collection::make($items);
> }
> ```
>
> 

##### 手动分页的实现

```php
<?php
use Illuminate\Pagination\LengthAwarePaginator;
use Illuminate\Pagination\Paginator;

	public function demo(){
  		$arr ;// 需要分页的数据为$arr 
  		$perPage = 5; // 每页显示5条数据
  
  		//判断是否有page参数 获取page参数 如果没有默认为第一页
        if ($request->has('page')) {     
            $current_page = $request->input('page');
            $current_page = $current_page <= 0 ? 1 :$current_page;
        } else {
            $current_page = 1;
        }
		//手动分割分页数据
        $item = array_slice($arr, ($current_page-1)*$perPage, $perPage);
        $total = count($arr);//数据总条数
  		//实例化 LengthAwarePaginator 类
        $arr  =new LengthAwarePaginator($item,$total, $perPage, $current_page, [
            'path' => Paginator::resolveCurrentPath(),                // 设置分页url地址
            'pageName' => 'page',
        ]);
        return view('admin.admin-mergeUser',['users'=>$arr]);//渲染数据
}		

```

#####视图渲染方法

```php+HTML
 <div class="am-fr">
      {{ $users->render()}}
  </div>
```

##### 如果分页样式有问题可以尝试自定义样式或者试试这段css

```css
<style type="text/css">
  #pull_right{
    text-align:center;
  }
  .pull-right {
    /*float: left!important;*/
  }
  .pagination {
    display: inline-block;
    padding-left: 0;
    margin: 20px 0;
    border-radius: 4px;
  }
  .pagination > li {
    display: inline;
  }
  .pagination > li > a,
  .pagination > li > span {
    position: relative;
    float: left;
    padding: 6px 12px;
    margin-left: -1px;
    line-height: 1.42857143;
    color: #428bca;
    text-decoration: none;
    background-color: #fff;
    border: 1px solid #ddd;
  }
  .pagination > li:first-child > a,
  .pagination > li:first-child > span {
    margin-left: 0;
    border-top-left-radius: 4px;
    border-bottom-left-radius: 4px;
  }
  .pagination > li:last-child > a,
  .pagination > li:last-child > span {
    border-top-right-radius: 4px;
    border-bottom-right-radius: 4px;
  }
  .pagination > li > a:hover,
  .pagination > li > span:hover,
  .pagination > li > a:focus,
  .pagination > li > span:focus {
    color: #2a6496;
    background-color: #eee;
    border-color: #ddd;
  }
  .pagination > .active > a,
  .pagination > .active > span,
  .pagination > .active > a:hover,
  .pagination > .active > span:hover,
  .pagination > .active > a:focus,
  .pagination > .active > span:focus {
    z-index: 2;
    color: #fff;
    cursor: default;
    background-color: #428bca;
    border-color: #428bca;
  }
  .pagination > .disabled > span,
  .pagination > .disabled > span:hover,
  .pagination > .disabled > span:focus,
  .pagination > .disabled > a,
  .pagination > .disabled > a:hover,
  .pagination > .disabled > a:focus {
    color: #777;
    cursor: not-allowed;
    background-color: #fff;
    border-color: #ddd;
  }
  .clear{
    clear: both;
  }
</style>
```

