# pomelo-client-node-ws
Pomelo 客户端的 Node.js 实现，适用于 `hybridconnector` 型 `connector`。

## Features
1. 支持多 pomelo 客户端实例的创建
2. 与浏览器端 pomelo 客户端保持相同 API
3. `wss` 协议支持
4. `protobuf` 支持
5. 客户端实例新增 `__CLIENT_ROUTE` 事件用于对所有服务器端推送消息进行统一处理

## Usage
```
npm i pomelo-client-node-ws --save
```

```javascript
const Pomelo = require('pomelo-client-node-ws');
const pomelo = new Pomelo();

pomelo.init({
    host: '192.168.1.20',
    port: 3010,
    scheme: 'ws'
}, () => {
    pomelo.request('gate.gateHandler.queryEntry', {
        uid: 234234232
    }, (data) => {
        console.log(data);
        pomelo.disconnect();
        if(data.code === 500) {
            console.error('gate 连接失败');
            return;
        }
        pomelo.init({
            host: data.hosts,
            port: data.port,
            scheme: 'wss'
        }, () => {
            // ...
        });
    });
});

pomelo.on('loginRes', (data) => {
    console.log(data);
});

// Feature: 对所有服务端推送消息的统一处理
pomelo.on('__CLIENT_ROUTE', (route, data) => {
    console.log(route);
    console.log(data);
});
```

## API

### client.init( prams, cb)
prams 参数
- host：string，需要连接的 connector 的 ip，如 '127.0.0.1'  
- port：int，需要连接的 connector 的端口号，如 6001  
- scheme，可选，string，协议类型，如 ws、wss。默认 ws  
- encode，可选，function，加密数据函数，默认官方的加密  
- decode，可选，function，解密数据函数，默认官方的解密
- user，可选  
- encrypt，可选，boolean，默认 false。  
- handshakeCallback，可选，默认 无。  
- maxReconnectAttempts，boolean，断线后最大自动重连次数，默认10次。  
- reconnect，可选，boolean，断线后是否自动重连，默认 false。
  
### client.request( route, msg, cb)
- route：string，请求的服务器接口  
- msg：Object，请求的参数，{ x : x , y : y }  
- cb：function，响应的回调函数
