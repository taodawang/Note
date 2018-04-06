ajax跨域方法：

1、禁止浏览器检查：

 ![QQ截图20180406145850](C:\Users\Administrator\Desktop\QQ截图20180406145850.png)

缺点：只能单一设置，适合接口调试等；



2、jsonp

动态创建script标签，只能发送get请求，请求成功后返回的类型为Content-type：‘Javascript’；后台要做对应的改动：与前台商定好回调函数的名称（callback）

缺点：服务器需要对应改动，只能发送get请求，发送的不是XHR请求。

3、CORS

​	浏览器在发送**普通请求**（get，post，head且无自定义头；Content-type为text/plain、multipart/form-data、application/x-www-form-urlencoded其中一种）的时候是先执行再判断是否为跨域请求，后台需要在调用方增加 acess-contrl-allow-origin:*;

​	如果是带cookie的跨域请求（被请求方的cookie），acess-contrl-allow-origin必须全匹配 ，且后台增加 acess-contrl-allow-credentials：true

​	比较好的解决办法是后台取到request的origin字段（浏览器在发现跨域请求的时候自动添加的），并在response中将acess-contrl-allow-origin设为 origin

4、带自定义头的跨域

参考上方的案例，可以将自定义头拿到，并在response中添加acess-contrl-allow-headers：headers

5、nginx/apche设置

6、隐藏跨域（后台无法修改相应设置的时候）

使用反向代理，请求的url都为同一个域名下的地址，所以浏览器认为不是跨域请求