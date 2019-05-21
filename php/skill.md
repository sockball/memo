*  私有构造函数 防止外界实例化对象
    ```
    private function __construct() {}
    ```

* 守护进程开启redis后 可进入redis-cli执行`shutdown`命令关闭

* 使用`file_get_contents`发送`GET`、`POST`请求
    ```
    // 发送GET请求, 以下可能返回 {"answer":"yes","forced":false,"image":"https://yesno.wtf/assets/yes/3-422e51268d64d78241720a7de52fe121.gif"}
    $result = file_get_contents('https://yesno.wtf/api')

    // 发送POST请求
    $context = stream_context_create([
        'http' => [
            'method' => 'POST',
            'header' => 'Content-Type: application/x-www-form-urlencoded;charset=utf-8' . "\r\n" .
                        'accept: application/json' . "\r\n" .
                        'Cookie: ...',
            'content' => '{"guid":"","data":[{"num":"CI12345678"}],"timeZoneOffset":-480}'
        ]
    ]);
    $result = file_get_contents('https://t.17track.net/restapi/track', false, $context);
    ```