# WEB

----

----

# 2020/01/29

----

## PHP unserialize()方法

+ serialize()将复杂变量“序列化[^1]”，unserialize()则将“序列化”得到的字符串“反序列化”为变量。  

	例:

```php
<?php

class Test
{
    public $a;
    public $abc;

    function __construct()
    {
        $a = 114;
        $abc = 514;
    }
}

$test = new Test();
$t = serialize($test);
echo $t;
?>
```

结果为：

```php
O:4:"Test":2:{s:1:"a";N;s:3:"abc";N;}
```

其中 ：  
O表示对象(Object),  
4为对象名长度,  
“Test”为对象名,  
2为对象内变量数  
。。。  



+ 漏洞：unserialize()在接受到不正确的变量个数时会跳过对象的magic方法[^2]\_\_wakeup()



[^1]:指将数组，对象等变量转换为易传输的字符串形式
[^2]:magic方法还有：\_\_construct()构造函数、\_\_destruct()析构函数、__sleep()（执行serialize()时，会先调用该函数）、\_\_clone()（对象复制**完成**时执行）



## get和post提交表单

+ get

	+ get是通过URL进行提交的，具体为格式为?(key1)=(value1)&(key2)=(value2)&...
	
	+ 因此安全性很低，通常不用来传输重要数据
	
	+ 由于url长度限制，数据一般很小
	
	+ 执行效率比post高
	
	+ 在php中通过$\_GET[key]来读取数据
	
+ post
	
	+ post不通过URL提交，而是将数据放在HTTP的请求包中
	
	+ 在php中通过$\_POST[key]来读取数据
	
+ $\_REQUEST

	>\$\_REQUEST用于存储\$\_POST和\$\_GET的数据总和，它们之间是相互独立的（即如果unset掉$_GET或者$_POST，$_REQUEST数组中的所存储的数据都不会发生改变）。当\$\_POST和中\$\_GET都有同名元素时，$_REQUEST所存储的数据会依据php配置文件 php.ini中配置的request_order="GP"（默认）来决定先存储GET数据再存储POST数据，最终的结果是POST数据覆盖掉了GET数据。如果改变request_order="PG"，结果会反过来。
	>
	>链接：http://www.imooc.com/article/33747





----

----

# 2020/01/31

----

## XEE

### XML**扩展标记语言**

+ 

### XEE





----

----

# 2020/02/01

----

## PHP is_numeric()函数

> is_numeric函数对于空字符%00，无论是%00放在前后都可以判断为非数值，而%20空格字符只能放在数值后。所以，查看函数发现该函数对对于第一个空格字符会跳过空格字符判断，接着后面的判断。

	也就是说

```php
if(!is_numeric($a) && $a == 1)
    echo "conflict!";
```

	当\$a=1%00时，会输出conflict
	
	当内容为算式时也可，如$a=1*1



注：使用该函数来做SQL参数限制可能会导致SQL注入漏洞。

----

## HTTP请求与响应

### HTTP请求

```http
GET / HTTP/1.1
Host: www.github.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:52.0) Gecko/20100101 Firefox/52.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Upgrade-Insecure-Requests: 1
Cookie: logged_in=yes;
Connection: close
```

上面是一个常见的HTTP请求：完整的请求通常由**消息头**，**空格**，**消息主体**。

![HTTP请求格式](.\Web.assets\http1.png)

第一行："GET"->请求方法，"/" ->资源路径(URL)，"HTTP/1.1"->HTTP协议版本

>Host： 客户机通过这个头告诉服务器，想访问的主机名。Host头域指定请求资源的Intenet主机和端口号，必须表示请求url的原始服务器或网关的位置。HTTP/1.1请求必须包含主机头域，否则系统会以400状态码返回。 
>
>User-Agent：User-Agent头域的内容包含发出请求的用户信息。浏览器类型，如果Servlet返回的内容与浏览器类型有关则该值非常有用。 
>
>Accept： 浏览器可接受的MIME类型。
>
>Accept-Language：浏览器所希望的语言种类，当服务器能够提供一种以上的语言版本时要用到。
>
>Cookie：客户机通过这个头可以向服务器带数据，这是最重要的请求头信息之一。
>
>Connection：处理完这次请求后是否断开连接还是继续保持连接。如果Servlet看到这里的值为“Keep- Alive”，或者看到请求使用的是HTTP 1.1(HTTP 1.1默认进行持久连接)，它就可以利用持久连接的优点，当页面包含多个元素时(例如Applet，图片)，显著地减少下载所需要的时间。要实现这一点，Servlet需要在应答中发送一个Content-Length头，最简单的实现方法是：先把内容写入 ByteArrayOutputStream，然后在正式写出内容之前计算它的大小。

### HTTP响应

```http
HTTP/1.1 200 OK
Date: Tue, 26 Dec 2017 02:28:53 GMT
Content-Type: text/html; charset=utf-8
Connection: close
Server: GitHub.com
Status: 200 OK
Cache-Control: no-cache
Vary: X-PJAX
X-UA-Compatible: IE=Edge,chrome=1
Set-Cookie: user_session=37Q; path=/;
X-Request-Id: e341
X-Runtime: 0.538664
Content-Security-Policy: default-src 'none';
Strict-Transport-Security: max-age=31536000; includeSubdomains;
preload
Public-Key-Pins: max-age=0;
X-Content-Type-Options: nosniff
X-Frame-Options: deny
X-XSS-Protection: 1; mode=block
X-Runtime-rack: 0.547600
Vary: Accept-Encoding
X-GitHub-Request-Id: 7400
Content-Length: 128504
<!DOCTYPE html>
......
```

常见的状态码有：

>100~199：表示成功接收请求，要求客户端继续提交下一次请求才能完成整个处理过程。
>
>200~299：表示成功接收请求并已完成整个处理过程。常用200
>
>300~399：为完成请求，客户需进一步细化请求。例如：请求的资源已经移动一个新地址、常用302(意味着你请求我，我让你去找别人),307和304(我不给你这个资源，自己拿缓存)
>
>400~499：客户端的请求有错误，常用404(意味着你请求的资源在web服务器中没有)403(服务器拒绝访问，权限不够)
>
>500~599：服务器端出现错误，常用500

----

## XSS










