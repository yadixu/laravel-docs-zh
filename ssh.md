# SSH

- [设置档](#configuration)
- [基本用法](#basic-usage)
- [任务](#tasks)
- [SFTP 下载](#sftp-downloads)
- [SFTP 上传](#sftp-uploads)
- [编辑远程日志](#tailing-remote-logs)
- [Envoy 任务执行](#envoy-task-runner)

<a name="configuration"></a>
## 设置档

Laravel 可以简单的方式 SSH 连接到远程服务器并执行命令，让你可以简单在远程执行的建立 Artisan 任务。`SSH` facade 提供了使用方式让你连接到远程服务器并执行命令。

设置档放在 `config/remote.php`，里面包含所有需要设置的远程连接设置，`connections` 数组里有以服务器名称作为键值的列表。只要在 `connections` 数组设置好认证，你就准备好可以执行远程任务了。记得 `SSH` 可以经由密码或 SSH key 认证。

> **提示：** 需要在远程服务器执行很多任务吗？瞧瞧 [Envoy 任务执行](#envoy-task-runner)！

<a name="basic-usage"></a>
## 基本用法

#### 在在缺省服务器执行命令

使用 `SSH::run` 方法，在缺省的远程服务器执行命令：

	SSH::run([
		'cd /var/www',
		'git pull origin master',
	]);

#### 在特定服务器执行命令

你也可以使用 `into` 方法在特定的服务器上执行命令：

	SSH::into('staging')->run([
		'cd /var/www',
		'git pull origin master',
	]);

#### 捕捉命令的输出

你可以经由传入闭合函数到 `run` 方法，捕捉远程命令的即时输出：

	SSH::run($commands, function($line)
	{
		echo $line.PHP_EOL;
	});

<a name="tasks"></a>
## 任务

如果你需要定义一组一起执行的命令，你可以用 `define` 方法定义一个「任务」：

	SSH::into('staging')->define('deploy', [
		'cd /var/www',
		'git pull origin master',
		'php artisan migrate',
	]);

你可以用 `task` 方法执行定义过的任务：

	SSH::into('staging')->task('deploy', function($line)
	{
		echo $line.PHP_EOL;
	});

<a name="sftp-downloads"></a>
## SFTP 下载

`SSH` 类别里有简单的方式可以下载文件，使用 `get` 和 `getString` 方法：

	SSH::into('staging')->get($remotePath, $localPath);

	$contents = SSH::into('staging')->getString($remotePath);

<a name="sftp-uploads"></a>
## SFTP 上传

`SSH` 类别里也有简单的方式可以上传文件或甚至是字串到远程服务器，使用 `put` 和 `putString` 方法：

	SSH::into('staging')->put($localFile, $remotePath);

	SSH::into('staging')->putString($remotePath, 'Foo');

<a name="tailing-remote-logs"></a>
## 编辑远程日志

Laravel 有一个有用的命令，可以让你在任何远程服务器的 `laravel.log` 尾端附加日志内容。使用 Artisan 的 `tail` 命令以及指定远程连接的服务器名称：

	php artisan tail staging

	php artisan tail staging --path=/path/to/log.file

<a name="envoy-task-runner"></a>
## Envoy 任务执行

- [安装](#envoy-installation)
- [执行任务](#envoy-running-tasks)
- [多服务器](#envoy-multiple-servers)
- [平行执行](#envoy-parallel-execution)
- [任务巨集](#envoy-task-macros)
- [提醒通知](#envoy-notifications)
- [更新 Envoy](#envoy-updating-envoy)

Laravel Envoy 提供了简洁，轻量的语法，定义在远程服务器执行的共同任务。使用 [Blade](/docs/5.0/templates#blade-templating) 风格的语法，你可以简单的设置部署任务，执行 Artisan 命令或是更多。

> **提醒:** Envoy 需要 PHP 5.4 或更高的版本，并且只能在 Mac / Linux 操作系统下执行。

<a name="envoy-installation"></a>
### 安装

首先，使用 Composer `global` 命令安装 Envoy：

	composer global require "laravel/envoy=~1.0"

记得将 `~/.composer/vendor/bin` 路径加入 PATH，如此在终端机执行 `envoy` 命令时才找得到。

再来，在专案根目录建立 `Envoy.blade.php` 文件。这里有个范例可以让你作为起头：

	@servers(['web' => '192.168.1.1'])

	@task('foo', ['on' => 'web'])
		ls -la
	@endtask

如你所见，`@servers` 数组建立在文件的起始。你可以在宣告任务时，在 `on` 选项里参照这些服务器。在你的 `@task` 宣告里，写入想要在远程服务器执行的 Bash code。

`init` 命令可以简单的建立一个基本的 Envoy 档：

	envoy init user@192.168.1.1

<a name="envoy-running-tasks"></a>
### 执行任务

使用 `run` 命令去执行设置的任务：

	envoy run foo

如有需要，你可以传入参数到 Envoy 文件：

	envoy run deploy --branch=master

利用你所熟悉的 Blade 语法使用这些参数：

	@servers(['web' => '192.168.1.1'])

	@task('deploy', ['on' => 'web'])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

#### 启动码

你可以使用 ```@setup``` 语法，然后就能够在 Envoy 文件里宣告 PHP 变量，以及执行一般的 PHP 代码：

	@setup
		$now = new DateTime();

		$environment = isset($env) ? $env : "testing";
	@endsetup

你也可以使用 ```@include``` 引入 PHP 文件：

	@include('vendor/autoload.php');

<a name="envoy-multiple-servers"></a>
### 多服务器

你可以简单的在多个服务器执行任务。只要在任务宣告里列出服务器名称：

	@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

	@task('deploy', ['on' => ['web-1', 'web-2']])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

缺省上，任务会循序的在每个服务器上执行。意味着任务会在第一个服务器执行完后，才换到下一个。

<a name="envoy-parallel-execution"></a>
### 平行执行

如果你想在多个服务器上同时执行任务，只要简单的在任务宣告里加上 `parallel` 选项：

	@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

	@task('deploy', ['on' => ['web-1', 'web-2'], 'parallel' => true])
		cd site
		git pull origin {{ $branch }}
		php artisan migrate
	@endtask

<a name="envoy-task-macros"></a>
### 任务巨集

巨集让你可以只要使用一个命令，就能够循序执行一组任务。例如：

	@servers(['web' => '192.168.1.1'])

	@macro('deploy')
		foo
		bar
	@endmacro

	@task('foo')
		echo "HELLO"
	@endtask

	@task('bar')
		echo "WORLD"
	@endtask

现在 `deploy` 巨集可以经由一个简单的命令执行：

	envoy run deploy

<a name="envoy-notifications"></a>
<a name="envoy-hipchat-notifications"></a>
### 提醒通知

#### HipChat

你可能想要在执行完任务后，发送通知到团队的 HipChat 聊天室，使用简单的 `@hipchat` 宣告：

	@servers(['web' => '192.168.1.1'])

	@task('foo', ['on' => 'web'])
		ls -la
	@endtask

	@after
		@hipchat('token', 'room', 'Envoy')
	@endafter

你也可以自定发送到 hipchat 聊天室的消息，任何在 ```@setup``` 里宣告，或是经由 ```@include``` 引入的变量都可以使用在消息里：

	@after
		@hipchat('token', 'room', 'Envoy', "$task ran on [$environment]")
	@endafter

这是一个令人惊艳的简单方式，让你的团队保持通知在服务器执行的任务。

#### Slack

下面的语法可以发送通知到 [Slack](https://slack.com)：

	@after
		@slack('team', 'token', 'channel')
	@endafter

<a name="envoy-updating-envoy"></a>
### 更新 Envoy

执行 `self-update` 命令即可简单的更新 Envoy：

	envoy self-update

如果你的 Envoy 安装在 `/usr/local/bin`，你可能需要加上 `sudo`：

	composer global update
