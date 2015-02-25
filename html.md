# Forms & HTML

- [开启表单](#opening-a-form)
- [CSRF 保护](#csrf-protection)
- [表单模型绑定](#form-model-binding)
- [标签（ Label ）](#labels)
- [文本栏（ Text ）、多行文本栏（ Text Area ）、密码栏（ Password ）和隐藏栏（ Hidden Field ）](#text)
- [复选框（ Checkboxe ）和单选按钮（ Radio Button ）](#checkboxes-and-radio-buttons)
- [数字栏（ Number ）](#number)
- [文件输入](#file-input)
- [下拉式菜单](#drop-down-lists)
- [按钮](#buttons)
- [自定巨集（ Macro ）](#custom-macros)
- [产生 URL](#generating-urls)

<a name="opening-a-form"></a>
## 开启表单

#### 开启表单

	{{ Form::open(['url' => 'foo/bar']) }}
		//
	{{ Form::close() }}

缺省表单使用 POST 方法，当然你也可以指定其他方法：

	echo Form::open(['url' => 'foo/bar', 'method' => 'put'])

> **注意：**因为 HTML 表单只支持 `POST` 和 `GET` 方法，所以在使用 `PUT` 及 `DELETE` 方法时，Laravel 会自动加入 `_method` 隐藏字段到表单中，来伪装表单发送的方法。

你也可以建立指向命名路由或控制器动作的表单：

	echo Form::open(['route' => 'route.name'])

	echo Form::open(['action' => 'Controller@method'])

也可以传递路由参数：

	echo Form::open(['route' => ['route.name', $user->id]])

	echo Form::open(['action' => ['Controller@method', $user->id]])

如果表单允许上传文件，在数组中加上 `files` 选项：

	echo Form::open(['url' => 'foo/bar', 'files' => true])

<a name="csrf-protection"></a>
## CSRF 保护

#### 添加 CSRF Token 到表单

Laravel 提供简易的方法，让你可以保护你的应用程序免于跨网站请求伪造（ CSRF ）攻击。首先 Laravel 会自动在用户的 session 中存放随机产生的 token。如果你使用 `Form::open` 方法，并且选择 `POST`、`PUT` 或是 `DELETE`，CSRF token 会自动加到表单的一个隐藏字段中。但是，你如果想要替隐藏的 CSRF 字段产生 HTML，也可以使用 `token` 方法：

	echo Form::token();

#### 附加 CSRF 过滤到路由

	Route::post('profile', ['before' => 'csrf', function()
	{
		//
	}]);

<a name="form-model-binding"></a>
## 表单模型绑定

#### 开启模型表单

你经常会想要将模型内容加到表单中，可以使用 `Form::model` 方法实现：

	echo Form::model($user, ['route' => ['user.update', $user->id]])

现在当你产生表单元素时，像是 text 字段，模型属性和字段名称相符的属性值，会被自动设成字段值。举例来说，用户模型的 `email` 属性，将会设置到字段名称为 `email` 的 text 字段，不仅如此，当 Session 闪存数据中有与字段名称相符的数据，则会优先于模型的值被填入。优先级如下：

1. Session 闪存数据（旧输入数据）
2. 自行传入的数据
3. 模型属性数据

这让你可以快速建立表单，不仅是绑定模型数据，也可以在服务器端数据验证错误时，轻松的回填用户输入的旧数据！

> **注意：**当使用 `Form::model` 方法时，必须确保有使用 `Form::close` 方法来关闭表单！

<a name="labels"></a>
## 标签（ Label ）

#### 产生标签元素

	echo Form::label('email', 'E-Mail Address');

#### 指定额外 HTML 属性

	echo Form::label('email', 'E-Mail Address', ['class' => 'awesome']);

> **注意：**在建立标签后，当建立的表单元素名称与标签相符时，会自动建立与标签名称相同的 ID 属性。

<a name="text"></a>
## 文本栏（ Text ）、多行文本栏（ Text Area ）、密码栏（ Password ）和隐藏栏（ Hidden Field ）

#### 产生文本字段

	echo Form::text('username');

#### 自定默认值

	echo Form::text('email', 'example@gmail.com');

> **注意：** *hidden* 和 *textarea* 方法和 *text* 方法使用属性参数相同。

#### 产生密码字段

	echo Form::password('password');

#### 产生其他字段

	echo Form::email($name, $value = null, $attributes = []);
	echo Form::file($name, $attributes = []);

<a name="checkboxes-and-radio-buttons"></a>
## 复选框（ Checkboxe ）和单选按钮（ Radio Button ）

#### 产生复选框或单选按钮

	echo Form::checkbox('name', 'value');

	echo Form::radio('name', 'value');

#### 产生已选取的复选框或单选按钮

	echo Form::checkbox('name', 'value', true);

	echo Form::radio('name', 'value', true);

<a name="number"></a>
## 数字栏（ Number ）

#### 产生数字字段

	echo Form::number('name', 'value');

<a name="file-input"></a>
## 文件输入

#### 产生文件输入

	echo Form::file('image');

> **注意：**表单必须已将 `files` 选项设置为 `true`。

<a name="drop-down-lists"></a>
## 下拉式菜单

#### 产生下拉式菜单

	echo Form::select('size', ['L' => 'Large', 'S' => 'Small']);

#### 产生有默认值的下拉式菜单

	echo Form::select('size', ['L' => 'Large', 'S' => 'Small'], 'S');

#### 产生群组清单

	echo Form::select('animal', [
		'Cats' => ['leopard' => 'Leopard'],
		'Dogs' => ['spaniel' => 'Spaniel']
	]);

#### 产生数字区间的下拉式菜单

    echo Form::selectRange('number', 10, 20);

#### 产生月份名称的清单

    echo Form::selectMonth('month');

<a name="buttons"></a>
## 按钮

#### 产生提交按钮

	echo Form::submit('Click Me!');

> **提示：**需要产生按钮（ Button ）元素吗？试试看 *button* 方法。它与 *submit* 使用相同的属性参数。

<a name="custom-macros"></a>
## 自定巨集

#### 注册表单巨集（ Macro ）

定义称为「巨集」的表单类别的辅助方法是很轻松的。下面是它的运作方式。首先注册巨集，给定名称及闭合函示：

	Form::macro('myField', function()
	{
		return '<input type="awesome">';
	});

现在可以使用注册名称调用巨集：

#### 调用自定表单巨集

	echo Form::myField();

<a name="generating-urls"></a>
## 产生 URL

更多关于产生 URL 的信息，参考[辅助方法](/docs/5.0/helpers#urls)。
