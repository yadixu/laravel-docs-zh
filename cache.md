# 缓存

- [设定](#configuration)
- [缓存用法](#cache-usage)
- [递增与递减](#increments-and-decrements)
- [缓存标签](#cache-tags)
- [数据库缓存](#database-cache)

<a name="configuration"></a>
## 设定

Laravel 为各种不同的缓存系统提供一致的 API。 缓存配置文件存放在 `app/config/cache.php`。 您可以在此为应用程序指定使用哪一种缓存系统，Laravel 支持各种常见的后端缓存系统，如 [Memcached](http://memcached.org) 和 [Redis](http://redis.io)。

缓存配置文件也包含多个其他选项，在文件里都有说明，所以请务必先阅读过。Laravel 默认使用 `文件` 缓存系统，该系统会储存序列化、缓存对象在文件系统中。在大型应用程序中，建议使用储存在内存中的缓存系统，如 Memcached 或 APC。

<a name="cache-usage"></a>
## 缓存用法

#### 储存数据到缓存中

	Cache::put('key', 'value', $minutes);

#### 使用 Carbon 对象设定缓存过期时间

	$expiresAt = Carbon::now()->addMinutes(10);

	Cache::put('key', 'value', $expiresAt);

#### 若是数据不存在，则将其存入缓存中

	Cache::add('key', 'value', $minutes);

当数据确实被**加入**缓存时，使用 `add` 方法将会回传 `true` 否则会回传 `false`。

#### 检查缓存是否存在

	if (Cache::has('key'))
	{
		//
	}

#### 从缓存中取得数据

	$value = Cache::get('key');

#### 取得数据或是回传默认值

	$value = Cache::get('key', 'default');

	$value = Cache::get('key', function() { return 'default'; });

#### 永久储存数据到缓存中

	Cache::forever('key', 'value');

有时候您会希望从缓存中取得数据，而当此数据不存在时会储存默认值，您可以使用 `Cache::remember` 方法:

	$value = Cache::remember('users', $minutes, function()
	{
		return DB::table('users')->get();
	});

您也可以结合 `remember` 和 `forever` 方法:

	$value = Cache::rememberForever('users', function()
	{
		return DB::table('users')->get();
	});

请注意所有储存在缓存中的数据皆会被序列化，所以您可以任意储存各种类型的数据和对象。

#### 从缓存拉出数据

如果您需要从缓存中取得数据后将它删除，您可以使用 `pull` 方法:

	$value = Cache::pull('key');

#### 从缓存中删除数据

	Cache::forget('key');

<a name="increments-and-decrements"></a>
## 递增与递减

除了 `文件` 与 `数据库` 以外的缓存系统都支持 `递增` 和 `递减` 操作:

#### 递增值

	Cache::increment('key');

	Cache::increment('key', $amount);

#### 递减值

	Cache::decrement('key');

	Cache::decrement('key', $amount);

<a name="cache-tags"></a>
## 缓存标签

> **注意:** `文件` 或 `数据库` 这类缓存系统均不支持缓存标签. 此外, 使用带有 "forever" 的缓存标签时, 挑选 `memcached` 这类缓存系统将获得最好的性能, 它会自动清除过期的纪录。

#### 获取缓存标签

缓存标签允许您标记缓存内的相关数据，然后使用特定名称刷新所有缓存标签。要获取缓存标签可以使用 `tags` 方法。

您可以储存缓存标签，通过将有序列表当作参数传入，或者作为标签名称的有序数组:

	Cache::tags('people', 'authors')->put('John', $john, $minutes);

	Cache::tags(array('people', 'artists'))->put('Anne', $anne, $minutes);

您可以结合使用各种缓存储存方法与标签，包含 `remember`, `forever` 和 `rememberForever`。您也可以从已标记的缓存中获取数据 ，以及使用其他缓存方法如 `increment` 和 `decrement`。

#### 从已标记的缓存中获取数据

要获取已标记的缓存，可传入相同的有序标签列表。

	$anne = Cache::tags('people', 'artists')->get('Anne');

	$john = Cache::tags(array('people', 'authors'))->get('John');

您可以刷新所有已标记的数据，使用指定名称或名称列表。例如，以下演示将会移除带有 `people` 或 `authors` 或者两者皆有的所有缓存标签，所以 "Anne" 和 "John" 皆会从缓存中被移除:

	Cache::tags('people', 'authors')->flush();

相对而言，以下演示将只会移除带有 `authors` 的标签，所以 "John" 会被移除，但是 "Anne" 不会。

	Cache::tags('authors')->flush();

<a name="database-cache"></a>
## 数据库缓存

当使用 `数据库` 缓存系统时，您必须设定一张数据库表来储存缓存数据。数据库表的 `Schema` 定义例子如下:

	Schema::create('cache', function($table)
	{
		$table->string('key')->unique();
		$table->text('value');
		$table->integer('expiration');
	});
