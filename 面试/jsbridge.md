<html>
<head>
​    <meta content="text/html; charset=utf-8" http-equiv="content-type">
​    <title>
​        js调用java
​    </title>
</head>

<body>
<p>
​    <div id="show"></div>
</p>


<p><input type="button" id="enter3" value="payInterface" onclick="payInterface();"/></p>

</body>
<script>

​        function setupWebViewJavascriptBridge(callback) {
​            if (window.WebViewJavascriptBridge) {
​                callback(WebViewJavascriptBridge)
​            } else {
​                document.addEventListener(
​                    'WebViewJavascriptBridgeReady'
​                    , function() {
​                        callback(WebViewJavascriptBridge)
​                    },
​                    false
​                );
​            }


​            if (window.WebViewJavascriptBridge) { return callback(WebViewJavascriptBridge); }
​            if (window.WVJBCallbacks) { return window.WVJBCallbacks.push(callback); }
​            window.WVJBCallbacks = [callback];
​            var WVJBIframe = document.createElement('iframe');
​            WVJBIframe.style.display = 'none';
​            WVJBIframe.src = 'wvjbscheme://__BRIDGE_LOADED__';
​            document.documentElement.appendChild(WVJBIframe);
​            setTimeout(function() { document.documentElement.removeChild(WVJBIframe) }, 0)
​        }

​        //在改function 中添加原生调起js方法
​        setupWebViewJavascriptBridge(function(bridge) {

​            //注册原生调起方法
​            //参数1： buttonjs 注册flag 供原生使用，要和原生统一
​            //参数2： data  是原生传给js 的数据
​            //参数3： responseCallback 是js 的回调，可以通过该方法给原生传数据
​            bridge.registerHandler("getUserInfos",function(data,responseCallback){

​                document.getElementById("show").innerHTML = "buuton js" + data;
​                responseCallback("button js callback");
​            });


​            document.getElementById('enter3').onclick = function (e) {
​            var data = "hello"
​            //参数1： pay 注册flag 供原生使用，要和原生统一
​            //参数2： 是调起原生时向原生传递的参数
​            //参数3： 原生调用回调返回的数据
​            bridge.callHandler('getBlogNameFromObjC',data,function(resp){
​                    document.getElementById("show").innerHTML = "payInterface" + resp;
​                }
​             );
​        }
​        })
</script>

</html>