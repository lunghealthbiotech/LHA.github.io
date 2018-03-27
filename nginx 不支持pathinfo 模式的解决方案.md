###nginx 不支持pathinfo 模式的解决方案

> 1.打开虚拟主机配置文件
>
> ```
> /usr/local/nginx/conf/vhost/www.xxx.com.conf
> ```
>
> 2.将 include other.conf 注释或删除
>
> 3.加入 include pathinfo.conf
>
> 4.配置重写规则
>
> ```shel
>  location / {
>  root /home/wwwroot/www.xxx.com/;#你的根路径
>  index index.html index.htm index.php;#文件优先权
>  if (!-e $request_filename) {
>  rewrite ^/index.php(.*)$ /index.php?s=$1 last; #应该就是说把请求为pathinfo模式的url 重写为？形式
>  rewrite ^(.*)$ /index.php?s=$1 last;
>  break;
>  }
>  }
> ```
>
> 5.重启服务器测试