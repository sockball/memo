Yii下使用workerman tcp长连接示例

配置
```
// common/config/main.php

'socket' => [
    'class' => \common\components\Socket::class,
    'protocol' => 'tcp',
    'ip' => '0.0.0.0',
    'port' => '2345'
]

// console/config/main.php
'db' => [
    'class' => \console\socket\Mysql::class,
]
```

console启动类
对于命令`./yii socket/server start -d`  
`$argv[0]`为`./yii`   
`$argv[1]`为`socket/server`  
`$argv[2]`为`start`
`$argv[3]`为`-d`

```
namespace console\controllers;

use Yii;
use yii\console\Controller;
use yii\helpers\Console;

/**
 * socket连接
 * 命令以 (php) ./yii socket/server 开头 以下为operator
 * start 挂起一个socket连接; -d 以守护进程挂起; stop 停止;
 * status 查看状态; restart 重启; reload 平滑重启; connections 查看连接状态
 *
 * @see http://doc.workerman.net/install/start-and-stop.html
 */
class SocketController extends Controller
{
    public $daemon;
    public $help;
    public $gracefully;
    // 设置后可省略命令为 './yii socket start -d'
    public $defaultAction = 'server';

    public function init()
    {
        global $argv;
        // 去除多余的$argv[0] yii
        array_shift($argv);
    }

    public function actionServer()
    {
        Yii::$app->socket->run();
    }

    public function options($actionID = '')
    {
        return ['daemon', 'gracefully', 'help'];
    }

    public function optionAliases()
    {
        return [
            'd' => 'daemon',
            'g' => 'gracefully',
            'h' => 'help'
        ];
    }
}
```

连接处理类
* 使用了woker定时器, 当空闲时间超过`DEFAULT_ACTIVATE_MYSQL_TIME`时间则重新连接mysql以免出现`mysql has gone way`2006错误
* 客户端在连接后`DEFAULT_AUTH_TIME`时间内不发送message则主动断开
* 客户端发送请求需要`ACTION_COLUMN`字段以触发相应方法
* SocketClient为需求类自定义编写...
```
namespace common\components;

use Yii;
use console\socket\SocketClient;
use console\socket\SocketResponse;
use Workerman\Connection\TcpConnection;
use Workerman\Lib\Timer;
use Workerman\Worker;
use yii\base\Component;

class Socket extends Component
{
    const DEFAULT_AUTH_TIME = 30;
//  const DEFAULT_ONLINE_TIME = 60;
    const DEFAULT_ACTIVATE_MYSQL_TIME = 14400;
    const ACTION_COLUMN = 'action';

    public $protocol;
    public $ip;
    public $port;
    public $worker_count = 4;

    public function initWorker()
    {
        $worker = new Worker("{$this->protocol}://{$this->ip}:{$this->port}");
        $worker->count = $this->worker_count;

        $worker->onWorkerStart = [$this, 'onWorkerStart'];
        $worker->onConnect = [$this, 'onConnect'];
        $worker->onMessage = [$this, 'onMessage'];
//        $worker->onClose = [$this, 'onClose'];

        Worker::runAll();
    }

    /**
     * @param Worker $worker
     */
    public function onWorkerStart($worker)
    {
        $this->setActivateMysqlTimer($worker);
    }

    /**
     * @param TcpConnection $connection
     */
    public function onConnect($connection)
    {
        $this->setAuthTimer($connection);
    }

    /**
     * @param TcpConnection $connection
     * @param string $data
     */
    public function onMessage($connection, $data)
    {
        if ($connection->auth_timer_id ?? false)
        {
            Timer::del($connection->auth_timer_id);
            $connection->auth_timer_id = null;
        }

        $data = json_decode($data);
        if ($this->basicValidate($data))
        {
            $action = $data->{self::ACTION_COLUMN};
            $client = new SocketClient();
            $client->$action($connection, $data);
        }
        else
        {
            $connection->send(SocketResponse::BadRequest());
            $connection->close();
        }
    }

    /**
     * @param TcpConnection $connection
     */
    public function onClose($connection)
    {

    }

    /**
     * 连接成功后设置一个验证定时器, 若指定时间内未发送消息则关闭连接
     * @param TcpConnection $connection
     */
    private function setAuthTimer($connection)
    {
        $connection->auth_timer_id = Timer::add(self::DEFAULT_AUTH_TIME, function () use ($connection) {
            $connection->send(SocketResponse::Timeout());
            $connection->close();
        }, null, false);
    }

    /**
     * 基本的认证：包括数据是否成功json_decode, 指定action是否存在
     * @param object $data
     * @return bool
     */
    private function basicValidate($data)
    {
        return is_object($data) && isset($data->{self::ACTION_COLUMN}) && is_callable([SocketClient::class, $data->{self::ACTION_COLUMN}]);
    }

    /**
     * 定时检测空闲的mysql连接并关闭
     * @param Worker $worker
     */
    private function setActivateMysqlTimer($worker)
    {
        Timer::add(self::DEFAULT_ACTIVATE_MYSQL_TIME, function () use ($worker) {
            if (Yii::$app->db->isActive && $this->ifCloseDb())
            {
                Yii::$app->db->close();
            }
        });
    }

    /**
     * 是否关闭数据库连接, 注意用于判定空闲的时间应稍微小于定时器时间(且一定小于mysql wait_timeout)
     * SHOW GLOBAL VARIABLES LIKE 'wait_timeout' 默认一般 28800s
     * @return bool
     */
    private function ifCloseDb()
    {
        return (time() - Yii::$app->db->_activeAt) > (self::DEFAULT_ACTIVATE_MYSQL_TIME - 300);
    }
}
```

Response返回体
```
namespace console\socket;

class SocketResponse
{
    public static function BadRequest()
    {
        return self::jsonFormat([
            'code' => 400,
            'msg' => '缺少有效参数'
        ]);
    }

    public static function Timeout()
    {
        return self::jsonFormat([
            'code' => 408,
            'msg' => '验证超时'
        ]);
    }

    public static function Online()
    {
        return self::jsonFormat([
            'code' => 200,
            'msg' => 'online'
        ]);
    }

    public static function Exception($msg, $code = 500)
    {
        return self::jsonFormat([
            'code' => $code,
            'msg' => $msg
        ]);
    }

    private static function jsonFormat($data)
    {
        return json_encode($data, JSON_UNESCAPED_UNICODE);
    }
}
```

重写了Connection 仅新增加了字段 用于判断重新连接mysql  
需在客户端处理请求类中(SocketClient)可能的地方刷新该值...
`Yii::$app->db->_activeAt = time()`  
暂未找到其他处理方法 讨论可见[此处](https://github.com/yiisoft/yii2/issues/12634)  
其中try catch的方法未能成功...(捕获不到 直接跳过 并在最外层捕获...)
```
namespace console\socket;

use yii\db\Connection;

class Mysql extends Connection
{
    public $_activeAt;
}
```
