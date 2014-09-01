# 安装

- [安装 Composer](#install-composer)
- [安装 Laravel](#install-laravel)
- [服务器环境需求](#server-requirements)
- [设定](#configuration)
- [优雅链结](#pretty-urls)

<a name="install-composer"></a>
## 安装 Composer

Laravel 框架使用 [Composer](http://getcomposer.org)来管理其相依性。首先，下载一份 `composer.phar` 下来。之后，您可以把它放在本地端的项目目录，或者是移至 `/usr/local/bin` 让全站皆可使用。在 Windows 下，您可以使用 Composer [Windows 安装工具](https://getcomposer.org/Composer-Setup.exe)。

<a name="install-laravel"></a>
## 安装 Laravel

### 通过 Laravel 安装工具

首先，下载 [Laravel PHAR 安装包](http://laravel.com/laravel.phar)。为了方便，将安装包改名为 `laravel` 并搬移到 `/usr/local/bin`。一旦安装完成，只要简单的 `laravel new` 命令，一个全新的 laravel 就会安装在您指定的目录中。例如，`laravel new blog` 将会创建一个目录为 `blog`，并在此目录中安装 laravel 及其相依扩展包。这方法安装将会比通过 Composer 安装要来得快许多。

### 通过 Composer Create-Project

您一样可以通过 Composer 在命令行执行 `create-project` 来安装 laravel: 

	composer create-project laravel/laravel --prefer-dist

### 通过下载

Composer 安装完成后，下载[最新版](https://github.com/laravel/laravel/archive/master.zip)的Laravel 框架并且解压缩到服务器上的一个目录中。接着，在 Laravel 应用程序的根目录下，执行 `php composer.phar install`（或者是 `composer install`）来将所有框架所需的相依扩展包安装完成。为了能够成功完成安装，您必须在服务器上安装好 Git。

如果您想要更新 Laravel 框架，您需要在命令行执行 `php composer.phar update` 来更新。

<a name="server-requirements"></a>
## 服务器环境需求

Laravel 框架有一些系统需求：

- PHP >= 5.4
- MCrypt PHP 扩展包

PHP 5.5 之后，一些发行版本需要手动安装 PHP JSON 扩展包。如果您使用的是 Ubuntu，可以通过 `apt-get install php5-json` 来直接安装。

<a name="configuration"></a>
## 设定

Laravel 几乎无需设定即可马上使用。您可以自由的开始开发。然而，您可以查看 `app/config/app.php` 文件和他的文件。它包含了数个您的应用程序所想要更动的选项如 `时区（timezone）` 和 `语系（locale）` 。

一旦 Laravel 安装完成，您应该同时[设定本地环境](/docs/configuration#environment-configuration)。当您在您的本机上部署时，可以让您得到更详细的错误信息。默认在您的正式环境里详细的错误信息是被关掉的。

> **附注:** 您不应该在正式环境中将 `app.debug` 设为 `true`。绝对！千万不要！

<a name="permissions"></a>
### 权限

Laravel 框架有一个目录需要额外设置权限：`app/storage` 需要让网页服务器有写入的权限。

<a name="paths"></a>
### 路径

一些框架的目录路径是可以被设定配置的。如果要更改这些目录的位置，请查看 `bootstrap/paths.php` 文件。

<a name="pretty-urls"></a>
## 优雅链结

### Apache

Laravel 框架通过 `public/.htaccess` 文件来让网址中不需要 `index.php`。如果您网页服务器是使用 Apache 的话，请确认您有开启 'mod_rewrite` 模组。

如果框架附带的 `.htaccess` 文件在 Apache 中无法作用，请尝试下面的版本：

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]


### Nginx

在 Nginx, 在您的网站设定中增加下面的设定将可以开启"优雅链接"：

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
