https比http多了一层 SSL/TLS加密协议，使得在进行http传输中的数据不容易被更改，下面演示一下如何用node快速搭建https服务器：

```
var https = require('https');
var fs = require('fs');

var options = {
    key: fs.readFileSync('ssh_key.pem'),
    cert: fs.readFileSync('ssh_cert.pem')
}

https.createServer(options,function(req,res){
        res.writeHead('200');
        res.end('Hello World!')
    })
    .listen('8001')
```