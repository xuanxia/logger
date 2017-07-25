# logger
h5调试

html 部分
 (function()
    {
        if ("WebSocket" in window)
        {
            // 打开一个 web socket
            window.wsLogger = new WebSocket("ws://localhost:8888");
            wsLogger.onopen = function()
            {
                // Web Socket 已连接上，使用 send() 方法发送数据
                console.log('已经连上ws服务器');
            };
           /* ws.onmessage = function (evt)
            {
                var received_msg = evt.data;
                console.log("数据已接收...");
            };*/
            wsLogger.onclose = function()
            {
                console.log("连接已关闭...");
            };
        }
        else
        {// 浏览器不支持 WebSocket
            console.log("您的浏览器不支持 WebSocket!");
        }
    })();
    function wsLog(message) {
        if(window.wsLogger && wsLogger.readyState === WebSocket.OPEN){
            /*
            * function 类型的
            * */
            var result = message;
            if(typeof message === 'function'){
                result = message.toString();
            }
            /*
            * 对象类型的
            *
            * */
            if(typeof message === 'object'){
                result = JSON.stringify(message);
            }
            wsLogger.send(result);
        }
    }
    
    
    服务器
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8888 });
wss.on('connection', function connection(ws) {
    ws.on('message', function incoming(message) {
        console.log('received: %s', message);
        wss.clients.forEach(function each(client) {
            if (client !== ws && client.readyState === WebSocket.OPEN) {
                client.send(message);
            }
        });
    });
});
