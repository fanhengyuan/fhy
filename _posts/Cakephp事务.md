title: Cakephp事务
date: 2016-03-16 15:25:00
tags: "php"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>
# 事务

> 要执行事务，模型所对应的表必须属于支持事务的数据源和类型。

> 所有的事务方法必须用模型的数据源对象来执行。要在模型中获得模型的数据源，请用：



    $dataSource = $this->getDataSource();

接着你就可以使用数据源来开始、提交或者回滚事务。

    $dataSource->begin();

    // 执行一些任务

    if (/*一切正常*/) {
        $dataSource->commit();
    } else {
        $dataSource->rollback();
    }

嵌套事务
--------

可以使用 :php:meth:`Datasource::begin()` 方法多次开始事务。只有当 ``commit`` 与 
``rollback`` 方法的调用次数与 ``begin`` 方法的调用次数相等时，事务才会结束::

    $dataSource->begin();
    // 执行一些任务
    $dataSource->begin();
    // 再执行几个任务
    if (/*最后的任务成功*/) {
        $dataSource->commit();
    } else {
        $dataSource->rollback();
        // 在主任务中改变一些东西
    }
    $dataSource->commit();

如果数据库支持嵌套事务、并且在数据源中开启嵌套事务支持，才会真的执行嵌套事务。如
果不支持嵌套事务或者嵌套事务支持被关闭，在事务模式中，这些方法总是会返回 true。

如果你想多次开始事务、但不使用数据库的嵌套事务，可以使用 
``$dataSource->useNestedTransactions = false;`` 来关闭嵌套事务支持。这会仅使用一
个全局事务。

实际的嵌套事务默认为关闭。使用 ``$dataSource->useNestedTransactions = true;`` 来
开启它。

## 测试例子：
```php
    /*Transactions Test*/
    public function tra_test(){
        $ds = $this->Article->getDataSource();

        $a_save = array(
                        'title' => 'Transactions Test',
                        'addtime' => time()
                        );
        $u_save = array(
                        'password' => 'Transactions',
                        'role' => 'admin21212',
                        'create_time' => date("Y-m-d H:i:s")
                        );

        # transactions start
        $ds->begin();
        $a_s_res = $this->Article->saveAll($a_save);
        $u_s_res = $this->User->saveAll($u_save);

        if($a_s_res && $u_s_res){
            echo 'commit';
            $ds->commit();
        }else{
            echo 'rollback';
            $ds->rollback();            
        }
        # transactions end
    }
```
## 测试二（嵌套事务）
```php
        $o_flag = $og_flag = $o_flag2 = $flag2 = false; # 记录事务
        # 开始事务嵌套事务
        $ds = $this->Orders->getDataSource();
        $ds->useNestedTransactions = true;
        $ds->begin();
        # 事务1
        $o_flag = $this->Orders->save($order_save_data);
        $og_flag = $this->OrdersGoods->save($og_save_data);
        $ds->begin();
        # 事务2
        $sum_change_price = (float)$this->OrdersGoods->field('sum(change_price) as sum_change_price', array('order_id' => $db_og['OrdersGoods']['order_id']));            
        $sum_change_price = -$sum_change_price;

        $order_save_data['failure_goods_money'] = $sum_change_price;
        $o_flag2 = $this->Orders->save($order_save_data);
        $og_db = $this->Orders->find('first', array('conditions' => array('id' => $db_og['OrdersGoods']['order_id'])));

        if($og_db['Orders']['goods_price'] - $og_db['Orders']['failure_goods_money'] < 0){
            $flag2 = false;
        }else{
            $flag2 = true;
        }

        if($o_flag && $og_flag && $o_flag2 && $flag2){
            $ds->commit();
            $this->_return_json_inf(200,'订单修改成功！');
        }else{
            $ds->rollback();
            $this->_return_json_inf(400,'订单修改失败！');
        }
        $ds->commit();
```

## 附：mysql数据库类型
``SHOW ENGINES``

|Engine             | Support | Comment                                                                    | Transactions | XA  | Savepoints |
|------------------ |:------: | -------------------------------------------------------------------------- | ------------ | --- | ---------- |
|ARCHIVE            | YES     | Archive storage engine                                                     | NO           | NO  | NO         |
|Aria               | YES     | Crash-safe tables with MyISAM heritage                                     | NO           | NO  | NO         |
|BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears)             | NO           | NO  | NO         |
|CSV                | YES     | CSV storage engine                                                         | NO           | NO  | NO         |
|FEDERATED          | YES     | FederatedX pluggable storage engine                                        | YES          | NO  | YES        |
|InnoDB             | DEFAULT | Percona-XtraDB, Supports transactions, row-level locking, and foreign keys | YES          | YES | YES        |
|MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables                  | NO           | NO  | NO         |
|MRG_MYISAM         | YES     | Collection of identical MyISAM tables                                      | NO           | NO  | NO         |
|MyISAM             | YES     | MyISAM storage engine                                                      | NO           | NO  | NO         |
|PERFORMANCE_SCHEMA | YES     | Performance Schema                                                         | NO           | NO  | NO         | 