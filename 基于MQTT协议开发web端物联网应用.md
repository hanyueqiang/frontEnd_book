#### 应用场景

在浏览器上点击按钮实现控制灯光、电视、窗帘等操作

#### 确定方案

通过服务端建立支持基于`WebSocket`的`MQTT`协议。使用`WebSocket`建立长连接，然后在`WebSocket`通道上，使用`MQTT`协议进行通信。通过调研和 demo 验证下方案可行。

#### 为什么使用 WebSocket

1.`WebSocket`是一种在单个`TCP`连接上进行全双工通讯的协议，同时允许跨域通讯 2.`WebSocket`使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在 `WebSocket API` 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输

#### 操作步骤

##### 1.注册

根据公司搭建的消息中心注册流程操作

必传参数:

```
  appKey: string
  udid: string 登录用户id
  timestamp: unix时间戳、时间偏差大于600s返回过期无效
```

##### 2.建立长连接

`mqtt.js`, 是用`JavaScript`编写同时遵循`MQTT`协议的客户端库，可用于`Node.js`和浏览器

gitHub: [mqtt.js](https://github.com/mqttjs/MQTT.js)

```
// 安装
npm install mqtt --save
或
yarn add mqtt
```

项目中引入

```
import mqtt from 'mqtt';

const options = {
  connectTimeout: 4000, // 超时时间 默认 30 * 1000毫秒，收到 CONNACK 之前等待的时间，即连接超时时间
  keepalive: 20, // 心跳时间，默认 60秒，设置 0 为禁用
  clean: true, // 设置为 false 在离线时接收 QoS 1 和 2 消息
  clientId: 'abc123wefsa234ddffefe', // 客户端Id 注册成功后返回的clientId
  username: 'dsdfewfs2912fd50254c798edfef', // 连接用户名 注册成功后返回的username
  password: '1dfeefwfsfdfec3f54119fabc123', // 连接密码 注册成功后返回的password
}

// ip：注册成功后返回的ip、端口：8083(非加密连接 ws:)、8084(加密连接 wss:)，此项目使用8083端口
const client = mqtt.connect('ws://ip.xx.xxx.61:8083/mqtt', options);

client.on('reconnect', (error) => {
  console.log('正在重连:', error)
});

client.on('error', (error) => {
  console.log('连接失败:', error)
});

```

##### 3.订阅/发布消息

```
// 连接成功后进行订阅、发布信息
client.on('connect', (e) => {
  console.log('连接成功');

  // subscribec: 注册成功后返回的topics下的subscribe
  this.client.subscribe(subscribe);

  // publish: 注册成功后返回的topics下的publish; publishMsg：要发送的内容
  client.publish(publish, publishMsg, (error) => {
    console.log(error || '发布成功')
  })

})

// 接收消息
client.on('message', (topic, message) => {
  console.log('收到消息');
  const response = JSON.parse(message.toString());
  console.log(response);
  // ... 业务处理
})

```

#### demo 地址

[MQTT over WebSocket](https://github.com/hanyueqiang/websocket_mqtt)
