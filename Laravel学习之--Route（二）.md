# Laravel学习之--Route（二）

------

昨天是看的[laravel中文社区](http://www.golaravel.com/)laravel5.2的东西，发现有些不一样，后来发现我装的是最新版5.3.29,今天看英文版的吧[laravel5.3](https://laravel.com/docs/5.3/)这个有点吃力看得懂不容易记，所以我是边看边操作:)

laravel5.3所有的路由定义在`routes\web.php`中

###控制器定义
将命令行切换到项目根目录下执行
```shell
php artisan make:controller TestController
```
上述命令将在`App\Http\Controllers`目录下创建TestController.php文件，内容如下：
```php
<?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class TestController extends Controller{
        //
    }
```
如果需要在`App\Http\Controllers`下创建子目录，再在子目录中创建controller可执行
```shell
php artisan make:controller Test\TestController
```
上述命令将在`App\Http\Controllers`目录下创建`Test\TestController`文件，其中内容如下：
```php
<?php

    namespace App\Http\Controllers\Test;

    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class TestController extends Controller{
       //
    }
```
注意比较这两个controller文件的namespace和use的内容。
###路由定义
```php
Route::any('test','TestController@index');
Route::any('test1','Test/TestController@index');
```
可以通过127.0.0.1:8000/test访问TestController下的index方法,通过127.0.0.1:8000/test1访问Test/TestController下的index方法。

还可以定义可以get传值的路由，又两中方式：
```php
Route::any('test/{id}','TestController@index');
Route::get('test/{id}','TestController@index');
```
上述两种方式都可以通过get传参数，URL格式：127.0.0.1:8000/test/3
则index方法为
```php
<?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class TestController extends Controller{
        public function index(){
            $id = $request->id;
        }
        
        //还可以是
        /*public function index($id = ""){
            $getid = $id;
        }
        其中index方法的参数名需要和定义路由的参数名保持一致，可以直接通过赋值的方式把URL中的id值传给$getid变量
        */
    }
```
###中间件的定义和使用
定义方式
```shell
php artisan make:middleware CheckTime
```
执行上述命令将在`App\Http\Middlewares`目录下创建文件CheckTime.php,内容如下：
```php
<?php

namespace App\Http\Middleware;

use Closure;

class CheckTime
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        $age = $request->age;
        if($age<=12){
            return redirect('demo');
        }else{
            return redirect('/');
        }
        return $next($request);
        
    }
}
```
定义了中间件还需要注册才能使用，在`App\Http`下的`Kernel.php`文件中注册
只需要在`$routeMiddleware`数组中添加一行
```php
'checktime' => \App\Http\Middleware\CheckTime::class,
```
上述代码表示注册了一个叫checktime的中间件，可以在中间件中定义访问逻辑，进行路由访问控制，利用中间件进行访问控制的路由定义方式如下：
```php
Route::group(['middleware'=>['checkage']], function() {
    Route::any('/primary',function(){
        return "primary school";
    });
    Route::any("/middle",function(){
        return "middle school";
    });
    
});
```
上述定义路由表示在访问 `127.0.0.1:8000/primary`或`127.0.0.1:8000/middle`是会自动执行中间件`CheckTime.php`中的业务逻辑**当业务逻辑为url跳转时无比不要跳转到`middle`或者`primary`中，这样很容易出现重定向次数过多的错误**

中间件和路由规则定义内容很多，英文文档不是特别好理解，需要多读几遍，我写出来的是我认为用到的比较多的，也是对于个人来说好理解的^_^
