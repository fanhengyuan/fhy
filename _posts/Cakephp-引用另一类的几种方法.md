title: Cakephp 引用另一类的几种方法
date: 2016-01-15 17:38:45
tags: "php"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>
## 项目实例：
```php
public function sayhello(){ # 调用另一控制器方法测试
	# 1 use import() for external libraries
	// App::import('Controller', 'Prize'); // The same as require('Controller/PrizeController.php');
	# 2 uses() for framework files
	// App::uses('PrizeController', 'Controller');
	# 3 include app/Vendor/Prize.php
	App::import('Vendor','Prize',array('file' => 'Prize.php'));
	$objprize = new PrizeController();
	echo $objprize->ajax_prize();
}
```

## 官方文档：
```php
# To load app/Vendor/flickr/flickr.php:
App::import('Vendor', 'flickr', array('file' => 'flickr/flickr.php'));

# To load app/Vendor/some.name.php:
App::import('Vendor', 'SomeName', array('file' => 'some.name.php'));

# To load app/Vendor/services/well.named.php:
App::import(
    'Vendor',
    'WellNamed',
    array('file' => 'services' . DS . 'well.named.php')
);

# To load app/Plugin/Awesome/Vendor/services/well.named.php:
App::import(
    'Vendor',
    'Awesome.WellNamed',
    array('file' => 'services' . DS . 'well.named.php')
);

# To load app/Plugin/Awesome/Vendor/Folder/Foo.php:
App::import(
    'Vendor',
    'Awesome.Foo',
    array('file' => 'Folder' . DS . 'Foo.php'));

# It wouldn’t make a difference if your vendor files are inside your /vendors directory. CakePHP will automatically find it.
# To load vendors/vendorName/libFile.php:
App::import(
    'Vendor',
    'aUniqueIdentifier',
    array('file' => 'vendorName' . DS . 'libFile.php')
);
```