## tp5中服务端跨域的实现方法

### 1. 通过Nginx服务器来实现跨域：

具体配置如下：

```shell
add_header Access-Control-Allow-Methods *;
#   预检命令的缓存，如果不缓存每次会发送两次请求
add_header Access-Control-Max-Age 3600;
#   带cookie请求需要加上这个字段，并设置为true
add_header Access-Control-Allow-Credentials true;
#   表示允许这个域跨域调用（客户端发送请求的域名和端口） 
#   $http_origin动态获取请求客户端请求的域   不用*的原因是带cookie的请求不支持*号
add_header Access-Control-Allow-Origin *;
#   表示请求头的字段 动态获取
add_header Access-Control-Allow-Headers $http_access_control_request_headers;
#   OPTIONS预检命令，预检命令通过时才发送请求
#   检查请求的类型是不是预检命令
 if ($request_method = OPTIONS){
    return 200;
 }
```

**优点：**

只通过更改nginx服务器的配置文件就可以实现跨域请求，不用修改任何php代码

**缺点：**

在用浏览器调试错误信息的时候，浏览器没法得到状态码，即会出现：

**fail to loaded response data**没有具体的错误信息。

### 2. 通过php代码代码配置实现跨域，在这里用tp5框架的构子行为来实现

具体配置如下：

在**api**模块下新建一个行为文件**Cros.php**，具体路径为：**app/api/behavior/Cros.php**

```php
<?php
namespace app\api\behavior;
class Cros {
  public function responseSend(&$params){
    header('Access-Control-Allow-Origin: *');     
    header('Access-Control-Allow-Methods: *');  
    header("Access-Control-Allow-Headers: token,Origin, X-Requested-With, Content-Type, Accept");
    header('Access-Control-Allow-Credentials:true');
    if (request()->isOptions()) {
      exit();
    }
  }
}
```

在**api**模块下的**tags.php**文件中添加配置，若没有**tags.php**文件，新建一下文件即可，内容如下：

```php
<?php
return [
  'response_send' => [
    'app\\api\\behavior\\Cros'
  ]
];
```

即可配置成功。

**优点：**

可以方便的调试错误信息

**缺点：**

配置有点麻烦，需要改php代码