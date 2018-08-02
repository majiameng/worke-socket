Workerman websocket 扩展
===============

## 安装
composer require tinymeng/think-worker dev-master

## 使用方法

#### 在项目<code> /application/index/controller </code>下创建文件Events.php

> 首先创建控制器类并继承 tinymeng\worker\Server，然后设置属性和添加回调方法

##### ThinkPhp框架示例如下：
~~~
<?php
/**
 * Name: Events.php.
 * Author: JiaMeng <666@majiameng.com>
 * Description: websocket callback
 */
namespace app\index\controller;

use tinymeng\worker\Server;
use GatewayWorker\Lib\Gateway;

class Events extends Server{
    /**
     * @var string Socket connect address
     */
    protected $socket = 'websocket://0.0.0.0:1314';
    /**
     * @var string The current class of namespace
     */
    protected $eventHandler = 'app\index\controller\Events';

    /**
     * Description:  当客户端连接时时触发
     * @param $client_id
     */
    public static function onConnect($client_id){
        echo 'client_id : '.$client_id. ', connect ' .PHP_EOL;
    }

    /**
     * Description:  当客户端发来消息时触发
     * Author: JiaMeng <666@majiameng.com>
     * @param int $client_id 连接id
     * @param string $data 具体消息
     * @return bool
     */
    public static function onMessage($client_id, $data) {
        echo 'client : '.$client_id. ',message data :'.$data .PHP_EOL;
    }

    /**
     * Description:  当客户端断开连接时触发
     * Author: JiaMeng <666@majiameng.com>
     * Updater:
     * @param int $client_id 连接id
     */
    public static function onClose($client_id) {
        echo 'client_id : '.$client_id .' close '.PHP_EOL;
    }

}
~~~

> 支持workerman所有的回调方法定义（回调方法必须是public static类型）


#### 启动文件

在应用根目录增加入口文件 socket.php

##### ThinkPhp框架示例如下：
~~~
#!/usr/bin/env php
<?php
/**
 * 用于检测业务代码死循环或者长时间阻塞等问题
 * 如果发现业务卡死，可以将下面declare打开（去掉//注释），并执行php socket.php reload
 * 然后观察一段时间workerman.log看是否有process_timeout异常
 */
//declare(ticks=1);

define('APP_PATH', __DIR__ . '/application/');

define('BIND_MODULE','index/Events');

// 加载框架引导文件
require __DIR__ . '/thinkphp/start.php';
~~~

##### Laravel框架示例如下：
~~~
#!/usr/bin/env php
<?php
/**
 * 用于检测业务代码死循环或者长时间阻塞等问题
 * 如果发现业务卡死，可以将下面declare打开（去掉//注释），并执行php socket.php reload
 * 然后观察一段时间workerman.log看是否有process_timeout异常
 */
//declare(ticks=1);

define('LARAVEL_START', microtime(true));

require __DIR__.'/vendor/autoload.php';

$app = require_once __DIR__.'/bootstrap/app.php';

$kernel = $app->make(App\Http\Controllers\EventsController::class);//Events类，根据自己的命名规范填写

~~~


##### 在命令行启动服务端 <code> php socket.php start </code>

```liunx

[root@izbp153yczpm4pp9pjs0u3z majiameng.com]# php socket.php start

Workerman[server.php] start in DEBUG mode
----------------------- WORKERMAN -----------------------------
Workerman version:3.5.13          PHP version:7.2.6
------------------------ WORKERS -------------------------------
user          worker              listen                    processes status
root          ChatBusinessWorker  none                       4         [OK] 
root          ChatGateway         websocket://0.0.0.0:1314   4         [OK] 
root          Register            text://0.0.0.0:1236        1         [OK] 
----------------------------------------------------------------
Press Ctrl+C to stop. Start success.

```

linux下面可以支持下面指令
~~~
php socket.php start|stop|status|restart|reload
~~~

#### 测试

在浏览器中进行客户端测试

> http://www.blue-zero.com/WebSocket/

输入socket地址  ws://IP:1314 测试socket服务是否正常