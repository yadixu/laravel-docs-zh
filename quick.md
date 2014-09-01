# Laravel 快速入门

- [安装](#installation)
- [本地开发环境](#local-development-environment)
- [路由](#routing)
- [建立视图](#creating-a-view)
- [建立迁移数据](#creating-a-migration)
- [Eloquent ORM](#eloquent-orm)
- [显示数据](#displaying-data)
- [部署应用](#deploying-your-application)

<a name="installation"></a>
## 安装

### 通过 Laravel 安装器

首先，下载 [Laravel 安装器 PHAR 包](http://laravel.com/laravel.phar)。为使用上方便，将文件搬移至 `/usr/local/bin` 并改名为 `laravel`。安装完成后，只要执行 `laravel new` 命令即可以创立一个全新的 laravel 项目在您指定的目录下。例如：`laravel new blog` 将会建立一个名为 `blog` 的目录，所需之相依扩展包的全新 laravel 项目安装其内。这个安装方式将会比通过 Composer 快许多。

### 通过 Composer

Laravel 框架使用 [composer](http://getcomposer.org) 来执行安装及相依性管理。如果还没有安装它的话，请先从 [安装 Composer](http://getcomposer.org/doc/00-intro.md) 开始吧。

安装之后，您可以通过命令行模式执行下列命令来安装 Laravel：

	composer create-project laravel/laravel your-project-name --prefer-dist

这个命令会下载并安装一份干净的 Laravel 在您目前所在目录的 `your-project-name` 的新建目录中。


如果您想要直接从 [Github 上的 Laravel Respoitory](https://github.com/laravel/laravel/archive/master.zip）手动下载一份 Laravel 也是可以的。只要在解压后的目录最顶层，执行 `composer install` 即可，这个命令会把框架相依的资源下载安装好。

### 权限配置

在安装 Laravel 之后，您需要让网页服务器有写入 `app/storage` 目录的权限。详情请见[安装过程](/docs/installation)文件说明。

### 运行 Laravel

一般而言，您需要一个网页服务器（如: Apache 或是 Nginx）来运行您的 Laravel 应用。如果您是使用 PHP 5.4 以上版本，那可以使用 PHP 内建的开发服务器，您只需要使用 Artisan 命令 `serve`：

	php artisan serve

<a name="directories"></a>
### 目录结构

安装完框架后，可以来了解熟悉一下项目的目录结构。`app` 目录里面包含了 `views（视图）`, `controllers（控制器）`, 还有 `models（模型）` 等目录。您的应用程序大多数的代码都会在这个目录中。您也会发现 `app/config` 这个目录，配置文件多存在在这目录之中。

<a name="local-development-environment"></a>
## 本地开发环境

过去您要在本机上配置一个本地的 PHP 开发环境是让人头痛的事情。要安装正确的 PHP 版本、必须的扩展包，还有所需的组件是废时耗力的。为了解决这状况，使用 [Laravel Homestead](/docs/homestead) 吧。Homestead 是以 Laravel 和 [Vagrant](http://vagrantup.com) 所设计的虚拟机器。而 Homestead Vagrant 封装预载建立一个完整 PHP 应用所需的所有软件。如此一来您可以在瞬间创建一个虚拟化、独立不受干扰的开发环境。下面列出包装在 Homestead 里的软件：

- Nginx
- PHP 5.5
- MySQL
- Redis
- Memcached
- Beanstalk

不用担心，即使 "虚拟化" 听起来复杂，但这是无痛的。VirtualBox 和 Vagrant 是 Homestead 的依赖软件，您需要先安装他们。两个软件都有各平台的简单图形化安装接口。请参阅 [Homestead 文件](/docs/homestead) 进行了解。

<a name="routing"></a>
## 路由

一开始，我们先创建第一个路由。在 Laravel 中，最简单的路由是匿名函数路由。打开 `app/routes.php` 文件，并且增加下面的路由在文件的最下方：

	Route::get('users', function()
	{
		return 'Users!';
	});

现在，您在您浏览器中输入 `/users`，您应该会看到页面出现 `Users!`。很好！您已经建立了您的第一个路由。

路由也可以指向一个控制器类。例如：

	Route::get('users', 'UserController@getIndex');

这个路由告诉框架 `/users` 路由的请求应该使用 `UserController` 类的 `getIndex` 方法。查看更多控制器路由的信息，请查阅[控制器文件](/docs/controllers)。

<a name="creating-a-view"></a>
## 建立视图

接下来，我们要创建试图来显示我们的用户数据。视图以 HTML 代码存放在 `app/views` 的目录中。我们来存放两个视图进目录中： `layout.blade.php` 和 `user.blade.php`。首先，我们先来建立`layout.blade.php` 文件：

	<html>
		<body>
			<h1>Laravel Quickstart</h1>

			@yield('content')
		</body>
	</html>

接下来，我们建立 `users.blade.php` 视图：

	@extends('layout')

	@section('content')
		Users!
	@stop

这里有些语法或许让您感到陌生。因为我们使用的是 Laravel 的模板系统：Blade。Blade 非常快，仅需要少量的正规表示式来帮您的模板编译成 PHP 代码。Blade 提供了强大的功能，例如模板的继承，还有一些常用的 PHP 控制结构语法，如 `if` 和 `for`。更多信息请查阅 [Blade 文件](/docs/templates)。

现在，我们已经有了自己的视图，让我们回到 `/users` 路由。我们改用视图来替代显示出 `Users!`：

	Route::get('users', function()
	{
		return View::make('users');
	});

太棒了！现在您已经成功地建立了一个继承自 layout 的简单视图。接下来，我们开始到数据库层。

<a name="creating-a-migration"></a>
## 建立迁移文件

我们使用 Laravel 的迁移(migration)系统来建立数据库表以保存我们的数据。迁移记录著数据库的改变历程，这让团队成员间的信息分享更为简单。

首先，我们要配置数据库连接。您可以在 `app/config/database.php` 文件配置所有的数据库连接信息。默认中，Laravel 使用 MySQL，所以您必须将数据库连接信息填入其中。您也可以更改 `driver` 选项为 `sqlite`，如此他就会使用放置在 `app/database` 里的 SQLite 数据库。

接下来，我们来创建迁移文件，我们使用 [Artisan CLI](/docs/artisan)。在项目的根目录下，在终端里执行下列命令：

	php artisan migrate:make create_users_table

然后，在 `app/database/migrations` 目录下找到产生的迁移文件。文件中有一个包含了两个方法 `up` 和 `down` 的类。在 `up` 方法中，您必须表明您要对您的数据库表做哪些更动，而在 `down` 的方法里，您只要回溯这些修改。 

我们定义一个迁移文件如下：

	public function up()
	{
		Schema::create('users', function($table)
		{
			$table->increments('id');
			$table->string('email')->unique();
			$table->string('name');
			$table->timestamps();
		});
	}

	public function down()
	{
		Schema::drop('users');
	}

然后我们从终端里通过 `migrate` 命令来执行迁移动作。在项目的根目录里执行下列命令：

	php artisan migrate

如果您想回溯迁移，您可以执行 `migrate:rollback` 命令。现在我们已经建好了数据库表了，开始放些数据进去吧。

<a name="eloquent-orm"></a>
## Eloquent ORM

Laravel 提供了很棒的 ORM：Eloquent。如果您曾经使用过 Ruby on Rails 框架，那您将会觉得 Eloquent 很熟悉，因为它遵循 ActiveRecord ORM 风格的数据库互动模式。

首先，我们先来定义一个模型(model)。一个 Eloquent 模型可以用来查询关联的数据库表，以及表内的某一行。别担心，我们很快就会了解了。模型通常存放在 `app/models` 目录中。让我们先来在这目录里定义一个 `User.php` 的模型文件如下：

	class User extends Eloquent {}

注意，我们并未告诉 Eloquent 使用哪个表。Eloquent 有多种惯例，一种就是使用模型的复数形态作为该模型的数据库表名称，非常方便。

使用您喜欢的数据库管理工具，插入几笔数据到 `users` 数据库表，我们将使用 Eloquent 来取得这些数据并且传递到视图当中。

现在我们修改我们的 `/users` 路由，如下：

	Route::get('users', function()
	{
		$users = User::all();

		return View::make('users')->with('users', $users);
	});

我们来看看这个路由。首先，`User` 模型里的 `all` 方法会将 `users` 表里取得所有的记录。接下来，我们通过 `with` 方法将这些记录传递到视图里。`with` 方法接受一个键和与其对应的值，如此该键值就可以在视图中被使用了。

<a name="displaying-data"></a>
## 显示数据

现在，我们视图中已经可以获取到 `users` 类了，我们可以显示出来，如下：

	@extends('layout')

	@section('content')
		@foreach($users as $user)
			<p>{{ $user->name }}</p>
		@endforeach
	@stop

您会发现没有看到任何 `echo` 语句。当使用 Blade 时，您可以使用两个大括号来输出数据。很容易的，您应该可以通过 `/users` 路由来看到您的用户数据。

这仅仅只是开始。在这个教程中，您已经了解了 Laravel 基础部分，但是还有更多令人兴奋的东西等着您学习。继续阅读文件且更深入的了解 [Eloquent](/docs/eloquent) 和 [Blade](/docs/templates) 的强大特性。或许，您也更有兴趣去了解 [队列](/docs/queues) 和 [单元测试](/docs/testing)。也或许，您更想要了解 [IoC Container] 来展现实力，选择在您。

<a name="deploying-your-application"></a>
## 部署应用程序

Laravel 的其中一个目标就是让 PHP 应用程序开发从下载到部署都轻松化，而 [Laravel Forge](https://forge.laravel.com) 提供了一个简单的方式去部署您的 Laravel 应用到快速的服务器上。Forge 可以配置并供应在 DigitalOcean、 Linode、Rackspace 和 Amazon EC2 上的机器群。如同 Homestead 一样，所有必须的最新版软件都已安装在内：Nginx、PHP 5.5、MySQL、Postgres、Redis、Memcached 等等。Forge 的 “快速部署” 可以让您在每次发布更新至 Github 或是 Bitbucket 时自动部署应用。

更重要的是，Forge 能帮助您配置 queue workers、SSL、Cron jobs、子网域等等。更多的信息请参阅 [Forge 网站](https://forge.laravel.com)。
