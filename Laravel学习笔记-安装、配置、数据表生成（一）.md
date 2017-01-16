# Laravel学习笔记-安装、配置、数据表生成（一）

------

今天算是第一天用**composer**，按照官方文档来学习laravel，以此作为终结，希望自己能每天坚持做笔记。
> * 我的操作环境
Mac OS X + XAMPP + composer
> * 操作过程
1.安装xampp，给htdocs文件夹所有操作权限chmod -R 777 htdocs
2.数据库管理工具使用的是[Sequel Pro](https://www.sequelpro.com/)
3.按照[laravel中文网](http://www.golaravel.com/tag/laravel-5-2/)站上的入门教程进行了安装

------
> * 学习总结

------
1.修改**Composer**默认镜像为国内镜像（原因都懂）
方法在[Composer中国全量镜像](http://pkg.phpcomposer.com/)
2.**Composer**创建项目[^code]
```python
composer create-project laravel\laravel laravel5 //将在项目目录下创建名为laravel5的文件夹，laravel框架代码都在laravel5目录下
```
3.启用**laravel**内置服务器[^code]
```
#将命令行切换到laravl5目录下
php artisan serve
#得到Laravel development server started on http://127.0.0.1:8000/即成功，通过浏览器访问127.0.0.1:8000即可看到效果
```
![index](http://7xlmi4.dl1.z0.glb.clouddn.com/2016-05-09-14622810192104.jpg)
```
#继续执行
php artisan make:auth
#访问127.0.0.1:8000/login得到如下效果
```
![](http://7xlmi4.dl1.z0.glb.clouddn.com/2016-05-09-14622965794477.jpg)
此时是不能注册的，当执行注册操作会报错，大概意识的说没有user表
4.配置服务器
在项目根目录下又一个.env文件，打开文件，可以找到数据库相关配置项，根据自己情况配置即可，配置完成后执行
```
php artisan migrate
```
这时候数据库中会增加一个users表，现在就可以注册啦^_^
5.数据表创建
数据表的创建不用在数据库管理软件中进行操作，只需要在命令行中操作即可,下面是创建学生信息表的例子
```
#命令行中执行
php artisan make:model Student //执行成功之后会在项目的／app目录下生成文件Student.php
php artisan make:migration create_table_student //执行成功之后会在／database／migrations下生成一个*create_table_student.php的文件，对文件的up方法做如下修改
Schema::create('students', function (Blueprint $table){//务必保证create第一参数是先前创建的model的复数形式
            $table->increments('id');//id自增
            $table->string("name");//string类型字段name
            $table->timestamps();//创建增加和修改时间字段
        });//保存，其他数据类型字段暂时不知道咋创建
#命令行中执行
php artisan migrate//执行成功创建表students
```
