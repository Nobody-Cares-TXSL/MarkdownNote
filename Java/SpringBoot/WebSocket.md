# maven坐标

```xml
<!-- websocket: 提供了对 WebSocket 协议的支持 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

# 简单的前后端WebSocket连接示例

## WebSocket 连接完整流程

### 连接建立阶段

前端初始化：

后端接收连接：

```plain&#x20;text
@OnOpen
public void onOpen(Session session, @PathParam("sid") String sid) {
    System.out.println("客户端：" + sid + "建立连接");
    sessionMap.put(sid, session);
}
```

连接确认：

### 消息交互阶段

前端发送消息：

```plain&#x20;text
function send(){
    var message = document.getElementById('text').value;
    websocket.send(message);
}
```

后端接收处理消息：

```plain&#x20;text
@OnMessage
public void onMessage(String message, @PathParam("sid") String sid) {
    System.out.println("收到来自客户端：" + sid + "的信息:" + message);
}
```

后端主动推送消息：

```plain&#x20;text
public void sendToAllClient(String message) {
    Collection<Session> sessions = sessionMap.values();
    for (Session session : sessions) {
        try {
            //服务器向客户端发送消息
            session.getBasicRemote().sendText(message);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

前端接收消息：

```plain&#x20;text
websocket.onmessage = function(event){
    setMessageInnerHTML(event.data);
}
function setMessageInnerHTML(innerHTML){
    document.getElementById('message').innerHTML += innerHTML + '<br/>';
}
```

### 连接关闭阶段

1. 主动关闭：

   * 用户点击 "关闭连接" 按钮，触发 `closeWebSocket()` 函数

   * 调用 `websocket.close()` 方法关闭连接

```plain&#x20;text
function closeWebSocket() {
    websocket.close();
}
```

1. 页面关闭时关闭连接：

   * 监听 `window.onbeforeunload` 事件，在页面关闭前主动关闭 WebSocket 连接

   * 防止连接未断开就关闭页面导致服务器抛出异常

```plain&#x20;text
window.onbeforeunload = function(){
    websocket.close();
}
```

后端处理关闭：

```plain&#x20;text
@OnClose
public void onClose(@PathParam("sid") String sid) {
    System.out.println("连接断开:" + sid);
    sessionMap.remove(sid);
}
```

前端关闭回调

# 简单的代码实现

## *注册WebSocket的ServerEndpointExporter*

```typescript
@Configuration
public class WebSocketConfiguration {
    /**
     * 注册WebSocket的ServerEndpointExporter
     * 扫描带有@ServerEndpoint注解的类，并注册成websocket服务
     *
     * ServerEndpointExporter是Spring框架提供的一个用于支持WebSocket的核心组件，它的主要作用是：
     * 扫描并注册WebSocket端点：自动检测Spring容器中带有@ServerEndpoint注解的类，并将它们注册为WebSocket端点
     * 创建和管理WebSocket实例：负责为每个WebSocket连接创建对应的端点实例
     * 连接生命周期管理：处理WebSocket连接的建立、消息传输和关闭等生命周期事件
     *
     * @return
     */
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
}
```

## **定义WebSocket服务端组件**

```typescript
@Component
@ServerEndpoint("/ws/{sid}")
public class WebSocketServer {
    //存放会话对象
    private static Map<String, Session> sessionMap = new HashMap();
    /**
     * 连接建立成功调用的方法
     */
    @OnOpen
    public void onOpen(Session session, @PathParam("sid") String sid) {
        System.out.println("客户端：" + sid + "建立连接");
        sessionMap.put(sid, session);
    }
    /**
     * 收到客户端消息后调用的方法
     *
     * @param message 客户端发送过来的消息
     */
    @OnMessage
    public void onMessage(String message, @PathParam("sid") String sid) {
        System.out.println("收到来自客户端：" + sid + "的信息:" + message);
    }
    /**
     * 连接关闭调用的方法
     *
     * @param sid
     */
    @OnClose
    public void onClose(@PathParam("sid") String sid) {
        System.out.println("连接断开:" + sid);
        sessionMap.remove(sid);
    }
    /**
     * 群发
     *
     * @param message
     */
    public void sendToAllClient(String message) {
        Collection<Session> sessions = sessionMap.values();
        for (Session session : sessions) {
            try {
                //服务器向客户端发送消息
                session.getBasicRemote().sendText(message);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 简单的前端

```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>WebSocket Demo</title>
</head>
<body>
<input id="text" type="text" />
<button onclick="send()">发送消息</button>
<button onclick="closeWebSocket()">关闭连接</button>
<div id="message">
</div>
</body>
<script type="text/javascript">
    var websocket = null;
    var clientId = Math.random().toString(36).substr(2);

    //判断当前浏览器是否支持WebSocket
    if('WebSocket' in window){
        //连接WebSocket节点
        websocket = new WebSocket("ws://localhost:8080/ws/"+clientId);
    }
    else{
        alert('Not support websocket')
    }

    //连接发生错误的回调方法
    websocket.onerror = function(){
        setMessageInnerHTML("error");
    };

    //连接成功建立的回调方法
    websocket.onopen = function(){
        setMessageInnerHTML("连接成功");
    }

    //接收到消息的回调方法
    websocket.onmessage = function(event){
        setMessageInnerHTML(event.data);
    }

    //连接关闭的回调方法
    websocket.onclose = function(){
        setMessageInnerHTML("close");
    }

    //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
    window.onbeforeunload = function(){
        websocket.close();
    }

    //将消息显示在网页上
    function setMessageInnerHTML(innerHTML){
        document.getElementById('message').innerHTML += innerHTML + '<br/>';
    }

    //发送消息
    function send(){
        var message = document.getElementById('text').value;
        websocket.send(message);
    }

    //关闭连接
    function closeWebSocket() {
        websocket.close();
    }
</script>
</html>
```
