title: swoole 客户端 client
date: 2017-12-13 11:56:04
tags: "php"
---
<Excerpt in index | 首页摘要> 
swoole 两种类型的客户端 (-.-)<!-- more -->
<The rest of contents | 余下全文>

原文: [https://aiti.fun/860.html](https://aiti.fun/860.html)

```swoole_client``` 提供了 ```tcp/udp socket``` 的客户端的封装代码，使用时仅需 ```new swoole_client``` 即可。 
```swoole``` 的 ```socket client``` 对比 ```PHP``` 提供的 ```stream``` 族函数有哪些好处：

```stream``` 函数存在超时设置的陷阱和 ```Bug```，一旦没处理好会导致 ```Server ```端长时间阻塞
```fread``` 有8192长度限制，无法支持 ```UDP``` 的大包
```swoole_client``` 支持 ```waitall``` ，在知道包长度的情况下可以一次取完，不必循环取。
```swoole_client``` 支持 ```UDP connect```，解决了 ```UDP``` 串包问题
```swoole_client``` 是纯C的代码，专门处理 ```socket```，```stream``` 函数非常复杂。 ```swoole_client``` 性能更好
除了普通的同步阻塞+select的使用方法外，```swoole_client``` 还支持异步非阻塞回调。

以下例子非常简单，客户端向服务端发送一个请求。服务端收到后，返回当前距2018年春节剩余天数。

<h2>同步阻塞客户端</h2>

```
<?php

/**
 * 同步阻塞客户端
 * Class Client
 */
class Client
{
    private $client;

    public function __construct()
    {
        $this->client = new swoole_client(SWOOLE_SOCK_TCP);
    }

    public function connect()
    {
        if (!$this->client->connect("127.0.0.1", 9501, 1)) { # 后端 Server.php 中设置的ip和端口
            throw new Exception(sprintf('Swoole Error: %s', $this->client->errCode));
        }
    }

    public function send($data)
    {
        if ($this->client->isConnected()) {
            if (!is_string($data)) {
                $data = json_encode($data);
            }
            $this->client->send($data);
        } else {
            throw new Exception('Swoole Server does not connected.');
        }
    }

    public function close()
    {
        $this->client->close();
    }

    public function getRecv() {
        return $this->client->recv();
    }
}

$data = array(
    "url" => "http://192.168.1.112/send_mail",  # 这里为要执行任务的方法 比如发送邮件
    "param" => array(
        "username" => 'test',
        "password" => 'test'
    )
);

$client = new Client();
$client->connect();
$client->send($data);
echo $client->getRecv(); # 接受客户端返回
$client->close();
```

> ```php-fpm/apache``` 环境下只能使用同步客户端
> ```apache``` 环境下仅支持 ```prefork``` 多进程模式，不支持 ```prework``` 多线程

该例子执行返回如下：

```
# fhy at Centos7 in ~/myphp/swoole/test [15:51:42]
→ php SynClient.php 
69天16小时8分11秒
```
接收到服务端返回后，主动退出连接。

<h2>异步非阻塞客户端</h2>

```
<?php
/**
 * 异步非阻塞客户端
 */
class AsyncClient
{
    private $client;
    private $num = 0;
    public function __construct($ip, $port){
        $this->client = new swoole_client(SWOOLE_SOCK_TCP, SWOOLE_SOCK_ASYNC);

        $this->client->on('connect', array($this, 'onConnect'));
        $this->client->on('receive', array($this, 'onReceive'));
        $this->client->on('error', array($this, 'onError'));
        $this->client->on('close', array($this, 'onClose'));
        $this->client->connect($ip, $port);
    }

    public function onConnect(swoole_client $cli) {
        $cli->send("GET / HTTP/1.1\r\n\r\n");
    }

    public function onReceive(swoole_client $cli, $data) {
        echo "Receive: $data\n";
        $cli->send("GetTime");

//        $this->client->close();
        sleep(1);
    }

    public function onError(swoole_client $cli) {
        echo "error\n";
    }

    public function onClose(swoole_client $cli) {
        echo "Connection close\n";
    }
}

new AsyncClient('127.0.0.1', 9501);
```

返回信息：

```
# fhy at Centos7 in ~/myphp/swoole/test [15:51:49]
→ php AsyncClient.php 
Receive: 69天16小时8分6秒
Receive: 69天16小时8分6秒
Receive: 69天16小时8分5秒
Receive: 69天16小时8分4秒
Receive: 69天16小时8分3秒
Receive: 69天16小时8分2秒
Receive: 69天16小时8分1秒
Receive: 69天16小时8分
Receive: 69天16小时7分59秒
Receive: 69天16小时7分58秒
Receive: 69天16小时7分57秒
Receive: 69天16小时7分56秒
Receive: 69天16小时7分55秒
Receive: 69天16小时7分54秒
...
```

该客户端每隔一秒向服务端发送一个请求，服务端收到请求后返回信息。

> 异步客户端只能使用在 ```cli``` 命令行环境 异步的 ```swoole client ``` 的使用场景对于新手同学来说可能比较陌生，因为异步客户端是不可以应用在 ```apache``` 或 ```fpm``` 中的，而且仅能用于 ```cli``` 环境。
> 一种比较典型的使用场景就是你的后端服务器前面挡了一个网关服务器，网关和后端之间是通过内网 ```TCP``` 长链接方式通信，网关对所有前端实现 ```http``` 协议，那么，异步的 ```swoole client``` 此时就可以在网关服务器上得到价值实现。
> 具体来说，就是使用 ```swoole http server``` 实现一个常驻内存级的 ```http``` 服务器，然后在 ```swoole http server``` 中使用异步 ```client``` 连接后端服务器。

<h2>服务端程序</h2>

```
<?php
class Server
{
    private $serv;
    private $fd;
    private $num = 1;

    public function __construct()
    {
        $this->serv = new swoole_server("0.0.0.0", 9501);
        $this->serv->set(array(
            'worker_num' => 1, //一般设置为服务器CPU数的1-4倍
            'daemonize' => 1, //以守护进程执行
            'max_request' => 10000,
            'dispatch_mode' => 2,
            'task_worker_num' => 8, //task进程的数量
            "task_ipc_mode " => 3, //使用消息队列通信，并设置为争抢模式
            "log_file" => "./taskqueueu.log" ,//日志
        ));
        $this->serv->on('Receive', array($this, 'onReceive'));
        // bind callback
        $this->serv->on('Task', array($this, 'onTask'));
        $this->serv->on('Finish', array($this, 'onFinish'));
        $this->serv->start();
    }

    public function onReceive(swoole_server $serv, $fd, $from_id, $data)
    {
        $this->fd = $fd;
        # echo "Get Message From Client {$fd}:{$data}\n";
        // send a task to task worker.

        // swoole-1.8.6或更高版本 回调
        $this->serv->task("taskcallback", -1, function (swoole_server $serv, $task_id, $res) {
            $this->serv->send($this->fd, $res);
        });

    }

    /**
     * 处理任务
     * @param $serv
     * @param $task_id
     * @param $from_id
     * @param $data
     * @return float|string
     */
    public function onTask($serv, $task_id, $from_id, $data)
    {
        $newYearTimes = strtotime(date("2018-02-16 00:00:00"));
        return $this->getStayTime($newYearTimes);
    }

    public function onFinish($serv, $task_id, $data)
    {
        echo "Task {$task_id} finish\n";
        echo "Result: {$data}\n";
    }

    function getStayTime($timestamp, $is_hour = 1, $is_minutes = 1)
    {
        if(empty($timestamp) || $timestamp <= 60) {
            return false;
        }
        $time = time();
        $remain_time = $timestamp - time();
        $day = floor($remain_time / (3600*24));
        $day = $day > 0 ? $day.'天' : '';
        $hour = floor(($remain_time % (3600*24)) / 3600);
        $hour = $hour > 0 ? $hour.'小时' : '';
        if($is_hour && $is_minutes) {
            $minutes = floor((($remain_time % (3600*24)) % 3600) / 60);
            $minutes = $minutes > 0 ? $minutes.'分' : '';
            $secend = floor( (($remain_time % (3600*24)) % 3600) % 60);
            $secend = $secend > 0 ? $secend.'秒' : '';
            return $day.$hour.$minutes.$secend;
        }
        if($hour) {
            return $day.$hour;
        }
        return $day;
    }
}
$server = new Server();
```