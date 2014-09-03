# 扩展包开发

- [简介](#introduction)
- [建立一个扩展包](#creating-a-package)
- [扩展包结构](#package-structure)
- [服务提供者](#service-providers)
- [缓载提供者](#deferred-providers)
- [扩展包惯例](#package-conventions)
- [开发工作流程](#development-workflow)
- [扩展包路由](#package-routing)
- [扩展包设定](#package-configuration)
- [扩展包视图](#package-views)
- [扩展包迁移](#package-migrations)
- [扩展包资源](#package-assets)
- [发布扩展包](#publishing-packages)

<a name="introduction"></a>
## 简介

扩展包是扩增 Laravel 的主要方式。扩展包可以是完成任何功能的数据，比方说处理时间的 [Carbon](https://github.com/briannesbitt/Carbon) 或是 BDD 测试框架如 [Behat](https://github.com/Behat/Behat)。

当然，目前有各式各样的扩展包。有些扩展包是独立运作 (stand-alone) 的，意思是指他们并不相依任何框架，包括 Laravel 。刚提到的 Carbon 及 Behat 就是这种扩展包。要使用这种扩展包只需要在 `composer.json` 文件里引入它们即可。

另一方面，有些扩展包特别指定要与 Laravel 整合。这种型式的扩展包在上一个版本的 Laravel 里称做 Bundle。这种扩展包可能有路由、控制器、视图、设定以及迁移，目标是增强 Laravel 本身的功能。由于没有特别需求要开发独立运作的扩展包，因此在这份指南里将主要以开发 Laravel 专属的扩展包为目标进行说明。

所有的 Laravel 扩展包都通过 [Packagist](http://packagist.org) 及 [Composer](http://getcomposer.org) 进行发布，因此学习如何使用这些美妙的 PHP 发布工具是一个必经的过程。

<a name="creating-a-package"></a>
## 建立一个扩展包

建立一个新扩展包最简单的方式就是通过 `workbench` 这个 artisan 命令。首先，您需要先在 `app/config/workbench.php` 里设定一些选项。在这个文件里，您会找到 `name` 及 `email` 这两个选项。这些选项里的值将会被用来产生扩展包里的 `composer.json` 档。当您设定好这些前置工作后，就已经可以开始打造您的 workbench 扩展包了！

#### 启始一个 workbench artisan 命令

	php artisan workbench vendor/package --resources

发行商 (vendor) 名称是为了识别不同作者发行相同名称的扩展包而设计的。比方说，我 (Taylor Otwell) 建立了一个新的扩展包名称为 "Zapper" ，而发行商的名称就是 `Taylor`。默认 workbench 命令会建立框架独立 (framework agnostic) 的扩展包结构。然后，`resources` 参数则会让 workbench 在产生扩展包结构时，额外针对 Laravel 产生特定的文件夹，包括 `migrations`、`views`、`config` 等。

当 `workbench` 命令被执行后，您的扩展包就可以在 `workbench` 文件夹内被获取。接着，您需要为您的扩展包`注册服务提供者 (ServiceProvider)`。您可以通过在 `app/config/app.php` 的 `providers` 数组里新增您扩展包的服务提供者名称进行注册，这个动作将会让 Laravel 启动时载入您的扩展包。服务提供者使用 `[Package]ServiceProvider` 这种命名惯例来为软件命名，以上述的例子来说，我们将会新增一行 `Taylor\Zapper\ZapperServiceProvider` 到 `providers` 数组里。

当提供者被注册后，您就已经完成扩展包开发的前置工作了！不过，建议您在深入开发工作前，先熟悉一下以下章节要接口的扩展包数据结构及开发工作流程。

> **注意：** 假如您的服务提供者无法被载入，记得在您的应用程序根目录底下执行 `php artisan dump-autoload` 命令。

<a name="package-structure"></a>
## 扩展包结构

当使用 `workbench` 命令后，您的扩展包将依照以下惯例进行初始化，这是为了让您的扩展包能与 Laravel 协同工作：

#### 基本的扩展包结构

	/src
		/Vendor
			/Package
				PackageServiceProvider.php
		/config
		/lang
		/migrations
		/views
	/tests
	/public

让我们继续探索下去。`src/Vendor/Package` 文件夹是存放这个扩展包所有的相关类代码，包括 `ServiceProvider`。而 `config`、`lang`、`migrations`、`views` 文件夹，就如同您预想的一样，存放关于您的扩展包的相关资源，一个扩展包可以包含任何种类的资源，就像「一般」的应用程序一样。

<a name="service-providers"></a>
## 服务提供者

服务提供者就是一个扩展包的启始类。默认来说，他包含两个方法：`boot` 和 `register`。通过这两个方法，您可以做任何您需要做的事，比方说：引入路由文件、注册 IoC 容器、注册事件或是任何其他您想要做的事。

`register` 这个方法则是当服务提供者被注册时马上被调用，而 `boot` 命令则是仅当一个请求被路由时才会被执行。所以，假如您的服务提供者相依于其他已经注册的服务提供者，或是您想要重写由其他服务提供者所做的绑定，则您应该使用 `boot` 方法。

当使用 `workbench` 新建一个扩展包时， `boot` 命令就已经包括以下动作：

	$this->package('vendor/package');

这个方法让 Laravel 知道如何载入视图、设定及其他您的应用程序所需的资源。在大多数的情况下，跟随这个 workbench 的惯例即可，并不需要特别修改这一行。

在默认的情况下，当注册完扩展包后，其资源就可以通过 `vendor/package` 取得。然后，您可以通过 package 方法的第二个参数来重写其动作，比方说：

	// 将 custom-namespace 传给 package 方法
	$this->package('vendor/package', 'custom-namespace');

	// 则扩展包资源可通过 custom-namespace 取得
	$view = View::make('custom-namespace::foo');

服务提供者类并没有「默认」的存放位置，您可以把它放在任何您喜欢的地方，或许可以将它们统一整理在 `Providers` 命名空间后，放到您的 `app` 文件夹里。只要 Composer [知道如何载入它们的话](http://getcomposer.org/doc/01-basic-usage.md#autoloading)，您就可以把这些文件放在任何地方。

假如您更改了您扩展包资源存放的位置，可能是配置文件或是视图，那您就应该在 `package` 方法调用时，传入第三个参数来指定您的资源位置：

	$this->package('vendor/package', null, '/path/to/resources');

<a name="deferred-providers"></a>
## 缓载提供者

假如您正在开发的服务提供者并没有注册任何形式的资源如配置文件或视图等，那您就可以考虑将您的服务提供者设定为「缓载」提供者。一个缓载提供者的特性就是只有需要它的功能时才载入，假如在当次的请求循环内不需要这个服务，则这个提供者就不会被载入。

要设定您的服务提供者采用缓载设定，只需要将 `defer` 属性设定为 `true` 即可：

	protected $defer = true;

接下来您应该从 `Illuminate\Support\ServiceProvider` 基础类重写 provides 方法，并回传一个数组包含所有您需要绑定到 IoC 容器的服务。举例来说，假如您的提供者注册 `package.service` 和 `package.another-service` 给 IoC 容器，您的 `provides` 方法应该长得像这样：

	public function provides()
	{
		return array('package.service', 'package.another-service');
	}

<a name="package-conventions"></a>
## 扩展包惯例

当想要取用扩展包的资源如配置文件或是视图时，需要如下的方法使用双分号来取得：

#### 载入扩展包的视图

	return View::make('package::view.name');

#### 取得扩展包的配置文件

	return Config::get('package::group.option');

> **注意：** 假如您的扩展包包括迁移，可以考虑在扩展包的迁移文件命名时加上您的扩展包名称，以避开迁移文件与其他扩展包在类命名时可能产生的冲突。

<a name="development-workflow"></a>
## 开发工作流程

当您开发一个扩展包时，通过如同开发一个应用程序一样的脉络来进行往往是很有帮助的，这样能让您简单的观看及实验您的样板等。因此，安装一个新的 Laravel 框架，然后使用 `workbench` 命令来建立您的扩展包结构来开始你的工作流程。

当 `workbench` 命令建立好您的扩展包结构后，您可以直接在 `workbench/[vendor]/[package]` 文件夹底下执行 `git init` 和 `git push` 进行开发过程的版本控制。这样可以让您很方便的在一个完整的应用程序脉络下开发您的扩展包，而不会受到 `composer update` 干扰而越陷越深。

既然您的扩展包在 `workbench` 文件夹内，您可能会很好奇 Composer 是怎么知道该如何载入您的扩展包文件？当 `workbench` 文件夹存在的话，Laravel 会很聪明的扫描底下的扩展包内容，并在应用程序启动时自动载入！

假如您需要重新产生您的扩展包自动载入文件，您可以使用 `php artisan dump-autoload` 命令。这个命令会重新产生自动载入文件在您的根目录项目内。

#### 执行 artisan 自动载入命令

	php artisan dump-autoload

<a name="package-routing"></a>
## 扩展包路由

在早先的 Laravel 里是使用 `handles` 的方式来指定哪些 URI 会由扩展包进行回应。但在 Laravel 4 里，一个扩展包可以对任何 URI 进行回应。在您的扩展包里载入路由文件，只需要在您的服务提供者的 `boot` 方法里 `include` 即可。

#### 从服务提供者引入一个路由文件

	public function boot()
	{
		$this->package('vendor/package');

		include __DIR__.'/../../routes.php';
	}

> **注意：** 假如您的扩展包有使用控制器，您会需要确定您的 `composer.json` 里的 auto-load 区块有正确的设定。

<a name="package-configuration"></a>
## 扩展包设定

#### 取用扩展包配置文件

有些扩展包需要配置文件，这些文件就如同应用程序配置文件一样的方式定义。而当您的服务提供者在注册资源时，通过 `$this->package` 方法后，即可通过「双冒号」语法取得其值：

	Config::get('package::file.option');

#### 取得扩展包的单一配置文件

假如您的扩展包仅包括一个单一配置文件，则您可以直接将文件名称命名为 `config.php`。当您完成这个步骤后，您就可以直接取得值而不需要指定名：

	Config::get('package::option');

#### 手动注册资源的命名空间

有时候您或许会希望可以 `$this->package` 方法外，注册一个扩展包资源 (或许是视图等)。一般来说，这通常只有在资源不是放在惯例的位置时才需要这样做。您可以在使用 `View`、`Lang`、`Config` 等类时，通过调用 `addNamespace` 方法来手动注册资源：

	View::addNamespace('package', __DIR__.'/path/to/views');

当命名空间被注册后，您就可以通过「双冒号」来取得资源：

	return View::make('package::view.name');

`addNamespace` 方法在 `View`、`Lang`、`Config` 等类上的实现是相同的。

### 串接设定

当其他的开发者安装了您的扩展包后，他们或许会希望可以重写部份的设定值。但假如他们直接修改您的扩展包代码，则当他们下次执行 composer update 时，这些设定值就又会被重写回来。取而代之，应该使用 `config:publish` 命令来发布配置文件：

	php artisan config:publish vendor/package

当这个命令被执行后，所有您的扩展包的配置文件都会被复制一份到 `app/config/packages/vendor/package` 底下，这样配置文件的内容就可以安全的被其他开发人员修改！

> **注意：** 开发者也有可能会建立环境配置文件，这些配置文件则会被放在 `app/config/package/vendor/package/environment` 底下。


<a name="package-views"></a>
## 扩展包视图

假如您在应用程序里使用扩展包，您偶尔会希望可以自定义扩展包的视图。您可以非常容易的通过 `view:publish` 命令将扩展包的视图输出到您的 `app/views` 文件夹底下。

	php artisan view:publish vendor/package

这个命令会将扩展包的视图移到 `app/views/packages` 文件夹内。假如这个文件夹还不存在的话，这个命令会自动帮您建立。当这些视图被输出后，您就可以依您所意进行调整。而当应用程序执行时，这些被调整过的视图就会被优先使用于扩展包的默认值。

<a name="package-migrations"></a>
## 扩展包迁移

#### 为扩展包进行迁移文件

您的扩展包可以非常容易的建立与执行迁移。当您想要为您的扩展包新增迁移文件时，只需要在命令后面加上 `--bench` 参数：

	php artisan migrate:make create_users_table --bench="vendor/package"

#### 为扩展包执行迁移

	php artisan migrate --bench="vendor/package"

#### 为已安装的扩展包执行迁移

为通过 Composer 安装的扩展包 (安装于 `vendor` 文件夹底下) 执行迁移，仅需加上 `--package` 参数：

	php artisan migrate --package="vendor/package"

<a name="package-assets"></a>
## 扩展包资源 Assets

#### 把扩展包资源移到 public

有些扩展包会带有诸多资源如 Javascript、CSS 和图片。然而，我们没有办法取用这些在 `vendor` 或 `workbench` 文件夹里的资源，所以我们需要一个把它们移到 `public` 文件夹的方法。而 `asset:publish` 这个命令就是为了这个目的而存在：

	php artisan asset:publish

	php artisan asset:publish vendor/package

假如扩展包仍在 `workbench` 阶段，记得加上 `--bench` 参数

	php artisan asset:publish --bench="vendor/package"

这个命令将会把资源依照供应商及扩展包名称移至 `public/packages` 底下。因此，一个叫 `userscape/kudos` 的扩展包资产就会被移到 `public/packages/userscape/kudos`。使用这个惯例可以让您安全地在您的扩展包视图中引入资源。

<a name="publishing-packages"></a>
## 发布扩展包

当您的扩展包已经准备好可以发布时，您应该先将您的扩展包送至 [Packagist](http://packagist.org) 。假如这个扩展包是专属于 Laravel 的话，您可以考虑在您的 `composer.json` 档里加上 `laravel` 的标签。

同时，标记您的版本对其他开发者来说也是很有用的。当开发者安装您的扩展包时，可以设定其偏好的版本 (stable, dev)。假如您的扩展包还没有稳定的版本，您可以考虑使用 `branch-alias`。

当您的扩展包发布后，仍可以继续通过原本的 `workbench` 应用程序脉络持续开发。这是当一个扩展包发布后，持续更新的最好方法。

有些组织选择自行建立他们内部使用的私有扩展包储存库给他们的开发者。假如您对这种作法也有兴趣，欢迎浏览由 Composer 释出的 [Satis](http://github.com/composer/satis) 项目文件。

