title: PHP 合并数组 追加数组例子
date: 2016-01-15 17:21:24
tags: "php"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>
	
	PHP合并数组我们可以使用array_merge()函数，array_merge()函数返回一个联合的数组。
	所得到的数组以第一个输入数组参数开始，按后面数组参数出现的顺序依次追加。

	其形式为：	
> array array_merge (array array1 array2…,arrayN)


## 下面是一个PHP合并数组的例子：
```php
<?php
	$fruits = array("apple","banana","pear");
	$numbered = array("1","2","3");
	$cards = array_merge($fruits, $numbered);
	print_r($cards);
	// 输出结果：
	// Array ( [0] => apple [1] => banana [2] => pear [3] => 1 [4] => 2 [5] => 3 )
?>
```
	用PHP追加数组，使用array_merge_recursive()，将两个数组合并在一起，注意，与array_merge()函数是不一样的，
	array_merge()的两个数组有重复项时会覆盖掉，而array_merge_recursive()则不会。
	array_merge_recursive()语法：
> array array_merge_recursive(array array1,array array2[…,array arrayN])
	

## 下面是一个PHP追加数组的例子：
```php
<?php
	$fruit1 = array("apple" => "red", "banana" => "yellow");
	$fruit2 = array("pear" => "yellow", "apple" => "green");
	$result = array_merge_recursive($fruit1, $fruit2);
	print_r($result);
	// 输出结果：
	// Array ( [apple] => Array ( [0] => red [1] => green ) [banana] => yellow [pear] => yellow )
?>
```

现在apple 指向一个数组，由两个颜色值组成的索引数组。