# Release Notes

- [Laravel 4.2](#laravel-4.2)
- [Laravel 4.1](#laravel-4.1)

<a name="laravel-4.2"></a>
## Laravel 4.2

此发行版本的完整更新列表可以从一个 4.2 的完整安装下，执行 `php artisan changes` 命令，或者 [Github 上的更新纪录](https://github.com/laravel/framework/blob/4.2/src/Illuminate/Foundation/changes.json)。此纪录仅含括主要的强化更新和此发行的更新部分。

> **附注:** 在 4.2 发布周期间，许多小的BUG修正与功能强化被整并至各个 4.1 的子发行版本中。所以最好确认 Laravel 4.1 版本的更新列表。 

### PHP 5.4 需求

Laravel 4.2 需要 PHP 5.4 以上的版本。此 PHP 更新版本让我们可以使用 PHP 的新功能：traits 来为如[Laravel 收银台](/docs/billing) 来提供更具表达力的接口。PHP 5.4 也比 PHP 5.3 带来显著的速度及性能提升。

### Laravel Forge

Larvel Forge，一个网页应用程序，提供一个简单的接口去建立管理您云端上的 PHP 服务器，如 Linode, DigitalOcean, Rackspace, 和 Amazon EC2。支持自动化 nginx 设定、SSH 金钥管理、Cron job 自动化、通过 NewRelic & Papertrail 服务器监控，"推送部署", Laravel queue worker 设定等等。Forge 提供最简单且更实惠的方式来部署所有您的 Laravel 应用程序。

默认 Laravel 4.2 的安装里， `app/config/database.php` 配置文件默认已为 Forge 设定完成，让在平台上的全新应用程序更方便部署。

关于 Laravel Forge 的更多信息可以在[官方 Forge 网站](https://forge.laravel.com)上找到。

### Laravel Homestead

Laravel Homestead 是一个为部署健全的 Laravel 和 PHP 应用程序的官方 Vagrant 环境。绝大多数的封装包的相依与软件在发布前已经部署处理完成，让封装包可以极快的被启用。Homestead 包含 Nginx 1.6, PHP 5.5.12, MySQL, Postres, Redis, Memcached, Beanstalk, Node, Gulp, Grunt 和 Bower。Homestead 包含一个简单的 `Homestead.yaml` 配置文件，让您在单一个封装包中管理多个 Laravel 应用程序。

默认的 Laravel 4.2 安装中包含的 `app/config/local/database.php` 配置文件使用 Homestead 的数据库作为默认。让 Laravel 初始化安装与设定更为方便。

官方文件已经更新并包含在 [Homestead 文件](/docs/homestead)中。

### Laravel 收银台

Laravel 收银台是一个简单、具表达性的资源库，用来管理 Stripe 的订阅帐务。虽然在安装中此组件依然是选用，我们依然将收银台文件包含在主要 Laravel 文件中。此收银台发布版本带来了数个错误修正、多货币支持还有支持最新的 Stripe API。

### Queue Workers 常驻软件

Artisan `queue:work` 命令现在支持 `--daemon` 参数让 worker 可以以"常驻软件"启用。代表 worker 可以持续的处理队列工作不需要重启框架。这让一个复杂的应用程序部署过程中，使得 CPU 的使用有显著的降低。

更多关于 Queue Workers 常驻软件信息请详阅 [queue 文件](/docs/queues#daemon-queue-worker)。

### Mail API Drivers

Laravel 4.2 为 `Mail` 函数采用了新的 Mailgun 和 Mandrill API 驱动。对许多应用程序而言，他提供了比 SMTP 更快也更可靠的方法来递送邮件。新的驱动使用了 Guzzle 4 HTTP 资源库。

### 软删除特性

得益于 PHP 5.4 traits , 一个更加简洁的软删除架构, 还有好多 "global scopes" , 都得以实现. 这些新架构为框架提供了更有扩展性的功能, 并且让框架更加简介.

更多关于软删除的信息请见: [Eloquent documentation](/docs/eloquent#soft-deleting).

### 更为方便的 认证(auth) & Remindable Traits

得益于 PHP 5.4 traits , 我们有了一个更简洁的 用户认真 和 密码提醒接口, 这也让 `User` 模型文件更加精简.

### "简易分页"

一个新的 `simplePaginate` 方法已被加入到查询以及 Eloquent 查询器中。让您在分页视图中，使用简单的"上一页"和"下一页"链接查询更为高效。

### 迁移确认

在正式环境中，破坏性的迁移动作将会被再次确认。如果希望取消提示字串确认请使用 `--force` 参数。

<a name="laravel-4.1"></a>
## Laravel 4.1

### 完整更新列表

此发行版本的完整更新列表，可以在版本 4.1 的安装中命令行执行 `php artisan changes` 取得，或者浏览 [Github 更新文件](https://github.com/laravel/framework/blob/4.1/src/Illuminate/Foundation/changes.json)中了解。其中只记录了该版本比较主要的强化功能和更新。

### 新的 SSH 组件

一个全新的 `SSH` 组件在此发行版本中登场。此功能让您可以轻易的 SSH 至远程服务器并执行命令。更多信息，可以参阅 [SSH 组件文件](/docs/ssh)。

新的 `php artisan tail` 命令就是使用这个新的 SSH 组件。更多的信息，请参阅 `tail` [命令集文件](http://laravel.com/docs/ssh#tailing-remote-logs)。

### Boris In Tinker

如果您的系统支持 [Boris REPL](https://github.com/d11wtq/boris)，`php artisan thinker` 命令将会使用到它。系统中也必须先行安装好 `readline` 和 `pcntl` 两个 PHP 扩展包。如果您没这些扩展包，从 4.0 之后将会使用到它。

### Eloquent 强化

Eloquent 新增了新的 `hasManyThrough` 关系链。想要了解更多，请参见 [Eloquent 文件](/docs/eloquent#has-many-through)。

一个新的 `whereHas` 方法也同时登场，他将允许[检索基于关系模型的约束](/docs/eloquent#querying-relations)。

### 数据库读写分离

Query Builder 和 Eloquent 目前通过数据库层，已经可以自动做到读写分离。更多的信息，请参考[文件](/docs/database#read-write-connections)。

### 队列排序

队列排序已经被支持，只要在 `queue:listen` 命令后将队列以逗号分隔送出。

### 失败队列工作处理

现在队列将会自动处理失败的工作，只要在 `queue:listen` 后加上 `--tries` 即可。更多的失败工作处理可以参见 [队列文件](/docs/queues#failed-jobs)。

### 缓存标签

缓存“区块”已经被“标签”取代。缓存标签允许您将多个“标签”指向同一个缓存对象，而且可以清空所有被指定某个标签的所有对象。更多使用缓存标签信息请见[缓存文件](/docs/cache#cache-tags)。

### 更具弹性的密码提醒

密码提醒引擎已经可以提供更强大的开发弹性，如：认证密码，显示状态信息等等。使用强化的密码提醒引擎，更多的信息[请参阅文件](/docs/security#password-reminders-and-reset)。

### 强化路由引擎

Laravel 4.1 拥有一个完全重新编写的路由层。API 一样不变。然而与 4.0 相比，速度快上 100%。整个引擎大幅的简化，且对于路由表达式的编译大大减少对 Symfony Routing 的依赖。

### 强化 Session 引擎

此发行版本中，我们亦发布了全新的 Session 引擎。如同路由增进的部分，新的 Session 曾更加简化且更快速。我们不再使用 Symfony 的 Session 处理工具，并且使用更简单、更容易维护的自定义解法。


### Doctrine DBAL

如果您有在您的迁移中使用到 `renameColumn`，之后您必须在 `composer.json` 里加 `doctrine/dbal` 进相依扩展包中。此扩展包不再默认包含在 Laravel 之中。