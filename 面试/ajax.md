具体来说，AJAX包括以下几个步骤。

1. 创建AJAX对象
2. 发出HTTP请求
3. 接收服务器传回的数据
4. 更新网页数据

概括起来，就是一句话，AJAX通过原生的`XMLHttpRequest`对象发出HTTP请求，得到服务器返回的数据后，再进行处理。

AJAX可以是同步请求，也可以是异步请求。但是，大多数情况下，特指异步请求。因为同步的Ajax请求，对浏览器有“堵塞效应”。

## XMLHttpRequest对象

`XMLHttpRequest`对象用来在浏览器与服务器之间传送数据。

```
var ajax = new XMLHttpRequest();
ajax.open('GET', 'http://www.example.com/page.php', true);

```

上面代码向指定的服务器网址，发出GET请求。

然后，AJAX指定回调函数，监听通信状态（`readyState`属性）的变化。

```
ajax.onreadystatechange = handleStateChange;

```

一旦拿到服务器返回的数据，AJAX不会刷新整个网页，而是只更新相关部分，从而不打断用户正在做的事情。

注意，AJAX只能向同源网址（协议、域名、端口都相同）发出HTTP请求，如果发出跨源请求，就会报错（详见《同源政策》和《CORS机制》两节）。

虽然名字里面有`XML`，但是实际上，XMLHttpRequest可以报送各种数据，包括字符串和二进制，而且除了HTTP，它还支持通过其他协议传送（比如File和FTP）。

下面是`XMLHttpRequest`对象的典型用法。

```
var xhr = new XMLHttpRequest();

// 指定通信过程中状态改变时的回调函数
xhr.onreadystatechange = function(){
  // 通信成功时，状态值为4
  if (xhr.readyState === 4){
    if (xhr.status === 200){
      console.log(xhr.responseText);
    } else {
      console.error(xhr.statusText);
    }
  }
};

xhr.onerror = function (e) {
  console.error(xhr.statusText);
};

// open方式用于指定HTTP动词、请求的网址、是否异步
xhr.open('GET', '/endpoint', true);

// 发送HTTP请求
xhr.send(null);

```

`open`方法的第三个参数是一个布尔值，表示是否为异步请求。如果设为`false`，就表示这个请求是同步的，下面是一个例子。

```
var request = new XMLHttpRequest();
request.open('GET', '/bar/foo.txt', false);
request.send(null);

if (request.status === 200) {
  console.log(request.responseText);
}

```

## XMLHttpRequest实例的属性

### readyState

`readyState`是一个只读属性，用一个整数和对应的常量，表示XMLHttpRequest请求当前所处的状态。

- 0，对应常量`UNSENT`，表示XMLHttpRequest实例已经生成，但是`open()`方法还没有被调用。
- 1，对应常量`OPENED`，表示`send()`方法还没有被调用，仍然可以使用`setRequestHeader()`，设定HTTP请求的头信息。
- 2，对应常量`HEADERS_RECEIVED`，表示`send()`方法已经执行，并且头信息和状态码已经收到。
- 3，对应常量`LOADING`，表示正在接收服务器传来的body部分的数据，如果`responseType`属性是`text`或者空字符串，`responseText`就会包含已经收到的部分信息。
- 4，对应常量`DONE`，表示服务器数据已经完全接收，或者本次接收已经失败了。

在通信过程中，每当发生状态变化的时候，`readyState`属性的值就会发生改变。这个值每一次变化，都会触发`readyStateChange`事件。

```
if (ajax.readyState == 4) {
  // Handle the response.
} else {
 // Show the 'Loading...' message or do nothing.
}

```

上面代码表示，只有`readyState`变为4时，才算确认请求已经成功，其他值都表示请求还在进行中。

### onreadystatechange

`onreadystatechange`属性指向一个回调函数，当`readystatechange`事件发生的时候，这个回调函数就会调用，并且XMLHttpRequest实例的`readyState`属性也会发生变化。

另外，如果使用`abort()`方法，终止XMLHttpRequest请求，`onreadystatechange`回调函数也会被调用。

```
var xmlhttp = new XMLHttpRequest();
xmlhttp.open( 'GET', 'http://example.com' , true );
xmlhttp.onreadystatechange = function () {
  if ( XMLHttpRequest.DONE != xmlhttp.readyState ) {
    return;
  }
  if ( 200 != xmlhttp.status ) {
    return;
  }
  console.log( xmlhttp.responseText );
};
xmlhttp.send();

```

### response

`response`属性为只读，返回接收到的数据体（即body部分）。它的类型可以是ArrayBuffer、Blob、Document、JSON对象、或者一个字符串，这由`XMLHttpRequest.responseType`属性的值决定。

如果本次请求没有成功或者数据不完整，该属性就会等于`null`。

### responseType

`responseType`属性用来指定服务器返回数据（`xhr.response`）的类型。

- ”“：字符串（默认值）
- “arraybuffer”：ArrayBuffer对象
- “blob”：Blob对象
- “document”：Document对象
- “json”：JSON对象
- “text”：字符串

text类型适合大多数情况，而且直接处理文本也比较方便，document类型适合返回XML文档的情况，blob类型适合读取二进制数据，比如图片文件。

```
var xhr = new XMLHttpRequest();
xhr.open('GET', '/path/to/image.png', true);
xhr.responseType = 'blob';

xhr.onload = function(e) {
  if (this.status == 200) {
    var blob = new Blob([this.response], {type: 'image/png'});
    // 或者
    var blob = oReq.response;
  }
};

xhr.send();

```

如果将这个属性设为ArrayBuffer，就可以按照数组的方式处理二进制数据。

```
var xhr = new XMLHttpRequest();
xhr.open('GET', '/path/to/image.png', true);
xhr.responseType = 'arraybuffer';

xhr.onload = function(e) {
  var uInt8Array = new Uint8Array(this.response);
  for (var i = 0, len = binStr.length; i < len; ++i) {
  // var byte = uInt8Array[i];
  }
};

xhr.send();

```

如果将这个属性设为“json”，支持JSON的浏览器（Firefox>9，chrome>30），就会自动对返回数据调用`JSON.parse()`方法。也就是说，你从xhr.response属性（注意，不是xhr.responseText属性）得到的不是文本，而是一个JSON对象。

XHR2支持Ajax的返回类型为文档，即xhr.responseType=”document” 。这意味着，对于那些打开CORS的网站，我们可以直接用Ajax抓取网页，然后不用解析HTML字符串，直接对XHR回应进行DOM操作。

### responseText

`responseText`属性返回从服务器接收到的字符串，该属性为只读。如果本次请求没有成功或者数据不完整，该属性就会等于`null`。

如果服务器返回的数据格式是JSON，就可以使用`responseText`属性。

```
var data = ajax.responseText;
data = JSON.parse(data);

```

### responseXML

`responseXML`属性返回从服务器接收到的Document对象，该属性为只读。如果本次请求没有成功，或者数据不完整，或者不能被解析为XML或HTML，该属性等于`null`。

返回的数据会被直接解析为DOM对象。

```
/* 返回的XML文件如下
  <?xml version="1.0" encoding="utf-8" standalone="yes" ?>
  <book>
      <chapter id="1">(Re-)Introducing JavaScript</chapter>
      <chapter id="2">JavaScript in Action</chapter>
  </book>
*/

var data = ajax.responseXML;
var chapters = data.getElementsByTagName('chapter');

```

如果服务器返回的数据，没有明示`Content-Type`头信息等于`text/xml`，可以使用`overrideMimeType()`方法，指定XMLHttpRequest对象将返回的数据解析为XML。

### status

`status`属性为只读属性，表示本次请求所得到的HTTP状态码，它是一个整数。一般来说，如果通信成功的话，这个状态码是200。

- 200, OK，访问正常
- 301, Moved Permanently，永久移动
- 302, Move temporarily，暂时移动
- 304, Not Modified，未修改
- 307, Temporary Redirect，暂时重定向
- 401, Unauthorized，未授权
- 403, Forbidden，禁止访问
- 404, Not Found，未发现指定网址
- 500, Internal Server Error，服务器发生错误

基本上，只有2xx和304的状态码，表示服务器返回是正常状态。

```
if (ajax.readyState == 4) {
  if ( (ajax.status >= 200 && ajax.status < 300)
    || (ajax.status == 304) ) {
    // Handle the response.
  } else {
    // Status error!
  }
}

```

### statusText

`statusText`属性为只读属性，返回一个字符串，表示服务器发送的状态提示。不同于`status`属性，该属性包含整个状态信息，比如”200 OK“。

### timeout

`timeout`属性等于一个整数，表示多少毫秒后，如果请求仍然没有得到结果，就会自动终止。如果该属性等于0，就表示没有时间限制。

```
  var xhr = new XMLHttpRequest();
  xhr.ontimeout = function () {
    console.error("The request for " + url + " timed out.");
  };
  xhr.onload = function() {
    if (xhr.readyState === 4) {
      if (xhr.status === 200) {
        callback.apply(xhr, args);
      } else {
        console.error(xhr.statusText);
      }
    }
  };
  xhr.open("GET", url, true);
  xhr.timeout = timeout;
  xhr.send(null);
}

```

### 事件监听接口

XMLHttpRequest第一版，只能对`onreadystatechange`这一个事件指定回调函数。该事件对所有情况作出响应。 XMLHttpRequest第二版允许对更多的事件指定回调函数。

- onloadstart 请求发出
- onprogress 正在发送和加载数据
- onabort 请求被中止，比如用户调用了`abort()`方法
- onerror 请求失败
- onload 请求成功完成
- ontimeout 用户指定的时限到期，请求还未完成
- onloadend 请求完成，不管成果或失败

```
xhr.onload = function() {
 var responseText = xhr.responseText;
 console.log(responseText);
 // process the response.
};

xhr.onerror = function() {
  console.log('There was an error!');
};

```

注意，如果发生网络错误（比如服务器无法连通），`onerror`事件无法获取报错信息，所以只能显示报错。

### withCredentials

`withCredentials`属性是一个布尔值，表示跨域请求时，用户信息（比如Cookie和认证的HTTP头信息）是否会包含在请求之中，默认为`false`。即向`example.com`发出跨域请求时，不会发送`example.com`设置在本机上的Cookie（如果有的话）。

如果你需要通过跨域AJAX发送Cookie，需要打开`withCredentials`。

```
xhr.withCredentials = true;

```

为了让这个属性生效，服务器必须显式返回`Access-Control-Allow-Credentials`这个头信息。

```
Access-Control-Allow-Credentials: true

```

`.withCredentials`属性打开的话，不仅会发送Cookie，还会设置远程主机指定的Cookie。注意，此时你的脚本还是遵守同源政策，无法 从`document.cookie`或者HTTP回应的头信息之中，读取这些Cookie。

## XMLHttpRequest实例的方法

### abort()

`abort`方法用来终止已经发出的HTTP请求。

```
ajax.open('GET', 'http://www.example.com/page.php', true);
var ajaxAbortTimer = setTimeout(function() {
  if (ajax) {
    ajax.abort();
    ajax = null;
  }
}, 5000);

```

上面代码在发出5秒之后，终止一个AJAX请求。

### getAllResponseHeaders()

`getAllResponseHeaders`方法返回服务器发来的所有HTTP头信息。格式为字符串，每个头信息之间使用`CRLF`分隔，如果没有受到服务器回应，该属性返回`null`。

### getResponseHeader()

`getResponseHeader`方法返回HTTP头信息指定字段的值，如果还没有收到服务器回应或者指定字段不存在，则该属性为`null`。

```
function getHeaderTime () {
  console.log(this.getResponseHeader("Last-Modified"));
}

var oReq = new XMLHttpRequest();
oReq.open("HEAD", "yourpage.html");
oReq.onload = getHeaderTime;
oReq.send();

```

如果有多个字段同名，则它们的值会被连接为一个字符串，每个字段之间使用“逗号+空格”分隔。

### open()

`XMLHttpRequest`对象的`open`方法用于指定发送HTTP请求的参数，它的使用格式如下，一共可以接受五个参数。

```
void open(
   string method,
   string url,
   optional boolean async,
   optional string user,
   optional string password
);

```

- `method`：表示HTTP动词，比如“GET”、“POST”、“PUT”和“DELETE”。
- `url`: 表示请求发送的网址。
- `async`: 格式为布尔值，默认为`true`，表示请求是否为异步。如果设为`false`，则`send()`方法只有等到收到服务器返回的结果，才会有返回值。
- `user`：表示用于认证的用户名，默认为空字符串。
- `password`：表示用于认证的密码，默认为空字符串。

如果对使用过`open()`方法的请求，再次使用这个方法，等同于调用`abort()`。

下面发送POST请求的例子。

```
xhr.open('POST', encodeURI('someURL'));
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.onload = function() {};
xhr.send(encodeURI('dataString'));

```

上面方法中，open方法向指定URL发出POST请求，send方法送出实际的数据。

下面是一个同步AJAX请求的例子。

```
var request = new XMLHttpRequest();
request.open('GET', '/bar/foo.txt', false);
request.send(null);

if (request.status === 200) {
  console.log(request.responseText);
}

```

### send()

`send`方法用于实际发出HTTP请求。如果不带参数，就表示HTTP请求只包含头信息，也就是只有一个URL，典型例子就是GET请求；如果带有参数，就表示除了头信息，还带有包含具体数据的信息体，典型例子就是POST请求。

```
ajax.open('GET'
  , 'http://www.example.com/somepage.php?id=' + encodeURIComponent(id)
  , true
);

// 等同于
var data = 'id=' + encodeURIComponent(id));
ajax.open('GET', 'http://www.example.com/somepage.php', true);
ajax.send(data);

```

上面代码中，`GET`请求的参数，可以作为查询字符串附加在URL后面，也可以作为`send`方法的参数。

下面是发送POST请求的例子。

```
var data = 'email='
  + encodeURIComponent(email)
  + '&password='
  + encodeURIComponent(password);
ajax.open('POST', 'http://www.example.com/somepage.php', true);
ajax.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
ajax.send(data);

```

如果请求是异步的（默认为异步），该方法在发出请求后会立即返回。如果请求为同步，该方法只有等到收到服务器回应后，才会返回。

注意，所有XMLHttpRequest的监听事件，都必须在`send()`方法调用之前设定。

`send`方法的参数就是发送的数据。多种格式的数据，都可以作为它的参数。

```
void send();
void send(ArrayBufferView data);
void send(Blob data);
void send(Document data);
void send(String data);
void send(FormData data);

```

如果发送`Document`数据，在发送之前，数据会先被串行化。

发送二进制数据，最好使用`ArrayBufferView`或`Blob`对象，这使得通过Ajax上传文件成为可能。

下面是一个上传`ArrayBuffer`对象的例子。

```
function sendArrayBuffer() {
  var xhr = new XMLHttpRequest();
  var uInt8Array = new Uint8Array([1, 2, 3]);

  xhr.open('POST', '/server', true);
  xhr.onload = function(e) { ... };
  xhr.send(uInt8Array.buffer);
}

```

FormData类型可以用于构造表单数据。

```
var formData = new FormData();

formData.append('username', '张三');
formData.append('email', 'zhangsan@example.com');
formData.append('birthDate', 1940);

var xhr = new XMLHttpRequest();
xhr.open("POST", "/register");
xhr.send(formData);

```

上面的代码构造了一个`formData`对象，然后使用send方法发送。它的效果与点击下面表单的submit按钮是一样的。

```
<form id='registration' name='registration' action='/register'>
    <input type='text' name='username' value='张三'>
    <input type='email' name='email' value='zhangsan@example.com'>
    <input type='number' name='birthDate' value='1940'>
    <input type='submit' onclick='return sendForm(this.form);'>
</form>

```

FormData也可以将现有表单构造生成。

```
var formElement = document.querySelector("form");
var request = new XMLHttpRequest();
request.open("POST", "submitform.php");
request.send(new FormData(formElement));

```

FormData对象还可以对现有表单添加数据，这为我们操作表单提供了极大的灵活性。

```
function sendForm(form) {
    var formData = new FormData(form);
    formData.append('csrf', 'e69a18d7db1286040586e6da1950128c');

    var xhr = new XMLHttpRequest();
    xhr.open('POST', form.action, true);
    xhr.onload = function(e) {
        // ...
    };
    xhr.send(formData);

    return false;
}

var form = document.querySelector('#registration');
sendForm(form);

```

FormData对象也能用来模拟File控件，进行文件上传。

```
function uploadFiles(url, files) {
  var formData = new FormData();

  for (var i = 0, file; file = files[i]; ++i) {
    formData.append(file.name, file); // 可加入第三个参数，表示文件名
  }

  var xhr = new XMLHttpRequest();
  xhr.open('POST', url, true);
  xhr.onload = function(e) { ... };

  xhr.send(formData);  // multipart/form-data
}

document.querySelector('input[type="file"]').addEventListener('change', function(e) {
  uploadFiles('/server', this.files);
}, false);

```

FormData也可以加入JavaScript生成的文件。

```
// 添加JavaScript生成的文件
var content = '<a id="a"><b id="b">hey!</b></a>';
var blob = new Blob([content], { type: "text/xml"});
formData.append("webmasterfile", blob);

```

### setRequestHeader()

`setRequestHeader`方法用于设置HTTP头信息。该方法必须在`open()`之后、`send()`之前调用。如果该方法多次调用，设定同一个字段，则每一次调用的值会被合并成一个单一的值发送。

```
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.setRequestHeader('Content-Length', JSON.stringify(data).length);
xhr.send(JSON.stringify(data));

```

上面代码首先设置头信息`Content-Type`，表示发送JSON格式的数据；然后设置`Content-Length`，表示数据长度；最后发送JSON数据。

### overrideMimeType()

该方法用来指定服务器返回数据的MIME类型。该方法必须在`send()`之前调用。

传统上，如果希望从服务器取回二进制数据，就要使用这个方法，人为将数据类型伪装成文本数据。

```
var xhr = new XMLHttpRequest();
xhr.open('GET', '/path/to/image.png', true);

// 强制将MIME改为文本类型
xhr.overrideMimeType('text/plain; charset=x-user-defined');

xhr.onreadystatechange = function(e) {
  if (this.readyState == 4 && this.status == 200) {
    var binStr = this.responseText;
    for (var i = 0, len = binStr.length; i < len; ++i) {
      var c = binStr.charCodeAt(i);
      var byte = c & 0xff;  // 去除高位字节，留下低位字节
    }
  }
};

xhr.send();

```

上面代码中，因为传回来的是二进制数据，首先用`xhr.overrideMimeType`方法强制改变它的MIME类型，伪装成文本数据。字符集必需指定为“x-user-defined”，如果是其他字符集，浏览器内部会强制转码，将其保存成UTF-16的形式。字符集“x-user-defined”其实也会发生转码，浏览器会在每个字节前面再加上一个字节（0xF700-0xF7ff），因此后面要对每个字符进行一次与运算（&），将高位的8个位去除，只留下低位的8个位，由此逐一读出原文件二进制数据的每个字节。

这种方法很麻烦，在XMLHttpRequest版本升级以后，一般采用指定`responseType`的方法。

```
var xhr = new XMLHttpRequest();
xhr.onload = function(e) {
  var arraybuffer = xhr.response;
  // ...
}
xhr.open("GET", url);
xhr.responseType = "arraybuffer";
xhr.send();

```

## XMLHttpRequest实例的事件

### readyStateChange事件

`readyState`属性的值发生改变，就会触发readyStateChange事件。

我们可以通过`onReadyStateChange`属性，指定这个事件的回调函数，对不同状态进行不同处理。尤其是当状态变为4的时候，表示通信成功，这时回调函数就可以处理服务器传送回来的数据。

### progress事件

上传文件时，XMLHTTPRequest对象的upload属性有一个progress，会不断返回上传的进度。

假定网页上有一个progress元素。

```
<progress min="0" max="100" value="0">0% complete</progress>

```

文件上传时，对upload属性指定progress事件回调函数，即可获得上传的进度。

```
function upload(blobOrFile) {
  var xhr = new XMLHttpRequest();
  xhr.open('POST', '/server', true);
  xhr.onload = function(e) { ... };

  // Listen to the upload progress.
  var progressBar = document.querySelector('progress');
  xhr.upload.onprogress = function(e) {
    if (e.lengthComputable) {
      progressBar.value = (e.loaded / e.total) * 100;
      progressBar.textContent = progressBar.value; // Fallback for unsupported browsers.
    }
  };

  xhr.send(blobOrFile);
}

upload(new Blob(['hello world'], {type: 'text/plain'}));

```

### load事件、error事件、abort事件

load事件表示服务器传来的数据接收完毕，error事件表示请求出错，abort事件表示请求被中断。

```
var xhr = new XMLHttpRequest();

xhr.addEventListener("progress", updateProgress);
xhr.addEventListener("load", transferComplete);
xhr.addEventListener("error", transferFailed);
xhr.addEventListener("abort", transferCanceled);

xhr.open();

function updateProgress (oEvent) {
  if (oEvent.lengthComputable) {
    var percentComplete = oEvent.loaded / oEvent.total;
    // ...
  } else {
    // 回应的总数据量未知，导致无法计算百分比
  }
}

function transferComplete(evt) {
  console.log("The transfer is complete.");
}

function transferFailed(evt) {
  console.log("An error occurred while transferring the file.");
}

function transferCanceled(evt) {
  console.log("The transfer has been canceled by the user.");
}

```

### loadend事件

`abort`、`load`和`error`这三个事件，会伴随一个`loadend`事件，表示请求结束，但不知道其是否成功。

```
req.addEventListener("loadend", loadEnd);

function loadEnd(e) {
  alert("请求结束（不知道是否成功）");
}

```

## 文件上传

HTML网页的`<form>`元素能够以四种格式，向服务器发送数据。

- 使用`POST`方法，将`enctype`属性设为`application/x-www-form-urlencoded`，这是默认方法。

```
<form action="register.php" method="post" onsubmit="AJAXSubmit(this); return false;">
</form>

```

- 使用`POST`方法，将`enctype`属性设为`text/plain`。

```
<form action="register.php" method="post" enctype="text/plain" onsubmit="AJAXSubmit(this); return false;">
</form>

```

- 使用`POST`方法，将`enctype`属性设为`multipart/form-data`。

```
<form action="register.php" method="post" enctype="multipart/form-data" onsubmit="AJAXSubmit(this); return false;">
</form>

```

- 使用`GET`方法，`enctype`属性将被忽略。

```
<form action="register.php" method="get" onsubmit="AJAXSubmit(this); return false;">
</form>

```

某个表单有两个字段，分别是`foo`和`baz`，其中`foo`字段的值等于`bar`，`baz`字段的值一个分为两行的字符串。上面四种方法，都可以将这个表单发送到服务器。

第一种方法是默认方法，POST发送，Encoding type为application/x-www-form-urlencoded。

```
Content-Type: application/x-www-form-urlencoded

foo=bar&baz=The+first+line.&#37;0D%0AThe+second+line.%0D%0A

```

第二种方法是POST发送，Encoding type为text/plain。

```
Content-Type: text/plain

foo=bar
baz=The first line.
The second line.

```

第三种方法是POST发送，Encoding type为multipart/form-data。

```
Content-Type: multipart/form-data; boundary=---------------------------314911788813839

-----------------------------314911788813839
Content-Disposition: form-data; name="foo"

bar
-----------------------------314911788813839
Content-Disposition: form-data; name="baz"

The first line.
The second line.

-----------------------------314911788813839--

```

第四种方法是GET请求。

```
?foo=bar&baz=The%20first%20line.%0AThe%20second%20line.

```

通常，我们使用file控件实现文件上传。

```
<form id="file-form" action="handler.php" method="POST">
  <input type="file" id="file-select" name="photos[]" multiple/>
  <button type="submit" id="upload-button">上传</button>
</form>

```

上面HTML代码中，file控件的multiple属性，指定可以一次选择多个文件；如果没有这个属性，则一次只能选择一个文件。

file对象的files属性，返回一个FileList对象，包含了用户选中的文件。

```
var fileSelect = document.getElementById('file-select');
var files = fileSelect.files;

```

然后，新建一个FormData对象的实例，用来模拟发送到服务器的表单数据，把选中的文件添加到这个对象上面。

```
var formData = new FormData();

for (var i = 0; i < files.length; i++) {
  var file = files[i];

  if (!file.type.match('image.*')) {
    continue;
  }

  formData.append('photos[]', file, file.name);
}

```

上面代码中的FormData对象的append方法，除了可以添加文件，还可以添加二进制对象（Blob）或者字符串。

```
// Files
formData.append(name, file, filename);

// Blobs
formData.append(name, blob, filename);

// Strings
formData.append(name, value);    
```

append方法的第一个参数是表单的控件名，第二个参数是实际的值，第三个参数是可选的，通常是文件名。

最后，使用Ajax方法向服务器上传文件。

```
var xhr = new XMLHttpRequest();

xhr.open('POST', 'handler.php', true);

xhr.onload = function () {
  if (xhr.status !== 200) {
    alert('An error occurred!');
  }
};

xhr.send(formData);
```

目前，各大浏览器（包括IE 10）都支持Ajax上传文件。

除了使用FormData接口上传，也可以直接使用File API上传。

```
var file = document.getElementById('test-input').files[0];
var xhr = new XMLHttpRequest();

xhr.open('POST', 'myserver/uploads');
xhr.setRequestHeader('Content-Type', file.type);
xhr.send(file);


```

可以看到，上面这种写法比FormData的写法，要简单很多。