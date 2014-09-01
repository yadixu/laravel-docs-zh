# Artisan 命令行工具

- [简介](#introduction)
- [用法](#usage)

<a name="introduction"></a>
## 简介

Artisan 是 Laravel 内建的命令行工具，它提供了一些有用的命令协助您开发，它是由强大的 Symfony Console 组件所驱动。
<a name="usage"></a>
## 用法

#### 列出所有可用的命令

要查看所有可用的 Artisan 命令，您可以使用 `list` 这个命令:

	php artisan list

#### 查看命令的使用指南

每一个命令都包含一个 "使用指南" ，它显示并描述这个命令能够接受哪些参数和选项。要进入使用指南只需要在命令名称前面加上 `help`即可:


	php artisan help migrate

#### 指定配置环境

您可以指定配置环境，只要在执行命令时加上 `--env` 即可切换所指定的配置环境:

	php artisan migrate --env=local

#### 查看目前的 Laravel 版本

使用 `--version` 选项，您可以查看目前所使用的 Laravel 版本:

	php artisan --version
