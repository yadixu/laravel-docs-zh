# 数据库基本用法

- [配置文件](#configuration)
- [读写分离](#read-write-connections)
- [数据库操作](#running-queries)
- [数据库事务](#database-transactions)
- [获取连接](#accessing-connections)
- [数据库操作纪录](#query-logging)

<a name="configuration"></a>
## 配置文件

Laravel 让数据库连接与执行查询语句变得相当简单。数据库配置文件存放在 `app/config/database.php`。 您可以在此定义所需的数据库连接，也可以指定哪一个连接是默认的。所有支持的数据库类型与例子皆在文件内说明。

目前为止 Laravel 支持 4 种数据库系统: MySQL, Postgres, SQLite 和 SQL Server。

<a name="read-write-connections"></a>
## 读写分离

有时候您会需要使用一个数据库进行查询操作，另一个数据库负责新增、修改和删除操作。Laravel 使这件事变得轻而易举,且会自动使用适当的连接，不论您是使用原生查询、query builder 或是 Eloquent ORM。

要了解如何设定 读取 / 写入 连接, 请看以下例子:

	'mysql' => array(
		'read' => array(
			'host' => '192.168.1.1',
		),
		'write' => array(
			'host' => '196.168.1.2'
		),
		'driver'    => 'mysql',
		'database'  => 'database',
		'username'  => 'root',
		'password'  => '',
		'charset'   => 'utf8',
		'collation' => 'utf8_unicode_ci',
		'prefix'    => '',
	),

注意到有两个键值 `read` 和 `write` 已经被加到设定数组中。这两个键值都包含一个关键数组值 `host`。 而数据库 `read` 和 `write`的其他选项将会并入 `mysql` 的主要数组值内。所以,我们只需要定义 `read` 和 `write` 数组, 就可以重写主要数组的值。

因此，以这个例子来看 `192.168.1.1` 将会被当作 `读取` 数据库连接的地址，`192.168.1.2` 将会被当作 `写入` 数据库连接的地址。该数据库的凭证、前缀词、字符集和所有其他在 `mysql` 数组内的选项都将会在两个连接内共享。

<a name="running-queries"></a>
## 数据库操作

一但完成数据库连接设定后，即可使用 `DB` 类进行数据库操作。

#### 查询操作

	$results = DB::select('select * from users where id = ?', array(1));

方法 `select` 总是回传数组值。

#### 新增操作

	DB::insert('insert into users (id, name) values (?, ?)', array(1, 'Dayle'));

#### 修改操作

	DB::update('update users set votes = 100 where name = ?', array('John'));

#### 删除操作

	DB::delete('delete from users');

> **注意:**  `修改` 和 `删除` 会回传该次操作影响到的记录数。

#### 一般性操作

	DB::statement('drop table users');

#### 监听数据库操作事件

您可以使用 `DB::listen` 方法来监听数据库操作事件:

	DB::listen(function($sql, $bindings, $time)
	{
		//
	});

<a name="database-transactions"></a>
## 数据库事务

执行数据库交易中的一组操作，您可以使用 `transaction` 方法:

	DB::transaction(function()
	{
		DB::table('users')->update(array('votes' => 1));

		DB::table('posts')->delete();
	});

> **注意:** 在 `transaction` 闭包内抛出的任何错误皆会导致交易自动回滚到上一步。

有时候您需要自行初始化一项事务:

	DB::beginTransaction();

您可以使用 `rollback` 方法来将交易回滚到上一步:

	DB::rollback();

最后，您可以通过 `commit` 方法来提交事务:

	DB::commit();

<a name="accessing-connections"></a>
## 执行多个数据库连接

当使用多个连接时，您可以通过 `DB::connection` 方法获取它们:

	$users = DB::connection('foo')->select(...);

您也可以在底层 PDO 实体化之下获取原生查询，

	$pdo = DB::connection()->getPdo();

有时候您会需要重新连接至某个数据库:

	DB::reconnect('foo');

为了不超过 PDO 的 `max_connections` 最大连接数限制，您可以使用 `disconnect` 方法将某个数据库连接断开:

	DB::disconnect('foo');

<a name="query-logging"></a>
## 数据库操作记录

默认之下，Laravel 会将现有连接请求的所有操作记录都存放在内存中，然而，在某些情况下，如插入一组大量记录时，可能会导致应用程序使用内存过量。要将记录关闭，您可以使用 `disableQueryLog` 方法:

	DB::connection()->disableQueryLog();

要得到执行过的操作记录数组，您可以使用 `getQueryLog` 方法:

       $queries = DB::getQueryLog();
