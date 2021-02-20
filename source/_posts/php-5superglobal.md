---
title: php超级全局变量
date: 2020-10-16 17:13:09
tags:
- php
categories: 
- frontend
- php
---
　　PHP中预定义了几个超级全局变量（superglobals） ，这意味着它们在一个脚本的全部作用域中都可用。 你不需要特别说明，就可以在函数及类中使用。

> [详细介绍](https://www.runoob.com/php/php-superglobals.html)
<!-- more -->
# PHP 超级全局变量列表:
* $GLOBALS
* $_SERVER
* $_REQUEST
* $_POST
* $_GET
* $_FILES
* $_ENV
* $_COOKIE
* $_SESSION

# 本文具体介绍$_POST和$_GET
## $_POST
　　PHP $_POST 被广泛应用于收集表单数据，在HTML form标签的指定该属性："method="post"。

　　以下实例显示了两个输入字段（input）及提交按钮(submit)的表单(form)。 当用户通过点击 "Submit" 按钮提交表单数据时, 表单数据将发送至<form>标签中 action 属性中指定的脚本文件。 在这个实例中，我们指定文件来处理表单数据。如果你希望其他的PHP文件来处理该数据，你可以修改该指定的脚本文件名。 然后，我们可以使用超级全局变量 $_POST 来收集表单中的 input 字段数据:
### form.html 文件代码：
```html
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>
 
<form action="welcome.php" method="post">
名字: <input type="text" name="fname">
年龄: <input type="text" name="age">
<input type="submit" value="提交">
</form>
 
</body>
</html>
```
　　当用户填写完上面的表单并点击提交按钮时，表单的数据会被送往名为 "welcome.php" 的 PHP 文件：

### welcome.php 文件代码：
```php
欢迎<?php echo $_POST["fname"]; ?>!<br>
你的年龄是 <?php echo $_POST["age"]; ?>  岁。
```

## $_GET
　　PHP $_GET 同样被广泛应用于收集表单数据，在HTML form标签的指定该属性："method="get"。

　　$_GET 也可以收集URL中发送的数据。
### html代码
```html
<html>
<body>

<a href="test_get.php?subject=PHP&web=runoob.com">Test $GET</a>

</body>
</html>
```
　　当用户点击链接 "Test $GET", 参数 "subject" 和 "web" 将发送至"test_get.php",你可以在 "test_get.php" 文件中使用 $_GET 变量来获取这些数据。

### test_get.php
```php
<?php 
echo "Study " . $_GET['subject'] . " @ " . $_GET['web'];
?>
```
