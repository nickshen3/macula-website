---
title: "Macula Boot Starter for WebSocket"
linkTitle: "WebSocket"
weight: 22
---

## 概述

该模块基于[Spring Boot Websocket](https://docs.spring.io/spring-boot/docs/current/reference/html/messaging.html#messaging.websockets)搭建，支持基于[STOMP协议](https://docs.spring.io/spring-framework/reference/web/websocket.html)的Websocket通讯，通过Redis Pub/Sub实现Websocket跨实例通讯。



## 组件坐标

```xml
<dependency>
    <groupId>dev.macula.boot</groupId>
    <artifactId>macula-boot-starter-websocket</artifactId>
    <version>${macula.version}</version>
</dependency>
```



## 使用配置

### websocket服务模块配置

```yaml
macula:
  security:
    ignore-urls: /, /index.html, /hello2/**				# 演示项目忽略权限认证，正式环境要走授权逻辑
	websocket:
  enabled: true																		# 是否开启websocket自动配置，默认true
  permit-test: true   														# 是否允许测试白名单，/xxx/test/xxx的websocket路径默认开白，默认true
		endpoint: websocket 													# websocket端点，默认是websocket
 		broker-destination-prefixes: /topic, /queue		# 转发订阅的前缀，默认是/topic,/queue
		app-destination-prefixes: /app								# SimpAnnotationMethodMessageHandler的处理前缀，默认/app
		user-destination-prefix: /user								# UserDestinationMessageHandler的处理前缀，默认/user
    heartbeat: 10000, 10000												# 默认是10000,10000，前面是服务端心跳，后面是客户端心跳间隔
```

### 网关需要配置路由

```yaml
spring:
	gateway:
		routes:
			- id: macula-example-consumer-ws
        uri: lb:ws://macula-example-consumer			# 要加ws
        predicates:
          - Path=/websocket/**
        filters:
          - StripPrefix=1
macula:
  gateway:
    security:
      ignore-urls: /consumer/hello2/**						# 测试地址，正式环境建议使用授权
      only-auth-urls: /api/**, /websocket/**      # /websocket前缀在握手时会请求，这里要设置为仅认证
```



## 核心功能

本模块基于websocket之上使用STOMP协议实现广播、点对点消息发送功能，具体示例如下：

### WebSocket服务模块定义

#### index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Hello WebSocket</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
          integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
    <link href="/css/main.css" rel="stylesheet">
    <script src="https://code.jquery.com/jquery-3.1.1.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@stomp/stompjs@7.0.0/bundles/stomp.umd.min.js"></script>
    <script src="/js/app.js"></script>
</head>
<body>
<noscript><h2 style="color: #ff0000">Seems your browser doesn't support Javascript! Websocket relies on Javascript being
    enabled. Please enable
    Javascript and reload this page!</h2></noscript>
<div id="main-content" class="container">
    <div class="row">
        <div class="col-md-12">
            <div class="form-group">
                <label for="token">Access Token:</label>
                <input type="text" id="token" class="form-control" placeholder="Access Token?" />
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-md-4">
            <form class="form-inline">
                <div class="form-group">
                    <label for="connect">Connect:</label>
                    <button id="connect" class="btn btn-default" type="submit">Connect</button>
                    <button id="disconnect" class="btn btn-default" type="submit" disabled="disabled">Disconnect
                    </button>
                </div>
            </form>
        </div>
        <div class="col-md-8">
            <form class="form-inline">
                <div class="form-group">
                    <input type="text" id="name" class="form-control" placeholder="Your Name?">
                    <input type="text" id="sendTo" class="form-control" placeholder="Send To?">
                </div>
                <button id="send" class="btn btn-default" type="submit">Greeting</button>
                <button id="send2" class="btn btn-default" type="submit">Group</button>
                <button id="send3" class="btn btn-default" type="submit">OnlyMe</button>
                <button id="send4" class="btn btn-default" type="submit">Chat</button>
            </form>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
            <table id="conversation" class="table table-striped">
                <thead>
                <tr>
                    <th>Greetings</th>
                </tr>
                </thead>
                <tbody id="greetings">
                </tbody>
            </table>
        </div>
    </div>
</div>
</body>
</html>
```

#### app.js

```javascript
const stompClient = new StompJs.Client({
    onConnect: function (frame) {
        setConnected(true);
        console.log('Connected: ' + frame);

        // DEMO
        stompClient.subscribe('/topic/greetings', (greeting) => {
            showGreeting(JSON.parse(greeting.body).content);
        });

        // 订阅群组消息
        stompClient.subscribe('/topic/group/' + groupId, function (greeting) {
            showGreeting(JSON.parse(greeting.body).content);
        });

        // 订阅私人消息
        stompClient.subscribe('/user/queue/me', function (greeting) {
            showGreeting(JSON.parse(greeting.body).content);
        });

        // 订阅他人发给我的消息
        stompClient.subscribe('/user/queue/chat', function (greeting) {
            showGreeting(JSON.parse(greeting.body).content);
        });
    },

    // If disconnected, it will retry after 200ms
    reconnectDelay: 10000,

    debug: function (str) {
        console.log(str);
    },
    onWebSocketError: function (error) {
        console.error('Error with websocket', error);
        disconnect();
    },
    onDisconnect: function () {
        disconnect();
    },
    onStompError: function (frame) {
        console.error('Broker reported error: ' + frame.headers['message']);
        console.error('Additional details: ' + frame.body);
    }
});


function setConnected(connected) {
    $("#connect").prop("disabled", connected);
    $("#disconnect").prop("disabled", !connected);
    if (connected) {
        $("#conversation").show();
    } else {
        $("#conversation").hide();
    }
    $("#greetings").html("");
}

function connect() {
    let token = $("#token").val()
    if (token !== undefined && token.length > 0) {
        stompClient.brokerURL = 'ws://localhost:8000/websocket/websocket?access_token=' + token
        stompClient.activate();
    } else {
        alert("Please input access token!")
    }
}

function disconnect() {
    stompClient.deactivate();
    setConnected(false);
    console.log("Disconnected");
}

function sendName() {
    stompClient.publish({
        destination: "/app/hello",
        body: JSON.stringify({
            'name': $("#name").val()
        })
    });
}

function sendName2() {
    $.post("http://localhost:8000/consumer/hello2/" + groupId, {
        name: $("#name").val()
    });
}

function sendName3() {
    stompClient.publish({
        destination: "/app/me",
        body: JSON.stringify({
            'name': $("#name").val()
        })
    });
}

function sendName4() {
    stompClient.publish({
        destination: "/app/chat/" + $("#sendTo").val(),
        body: JSON.stringify({
            'name': $("#name").val()
        })
    });
}

function showGreeting(message) {
    $("#greetings").append("<tr><td>" + message + "</td></tr>");
}

$(function () {
    groupId = 123;
    $("form").on('submit', (e) => e.preventDefault());
    $("#connect").click(() => connect());
    $("#disconnect").click(() => disconnect());
    $("#send").click(() => sendName());
    $("#send2").click(() => sendName2());
    $("#send3").click(() => sendName3());
    $("#send4").click(() => sendName4());
});
```

#### WebSocketController

```java
@RestController
@RequiredArgsConstructor
@Slf4j
public class WebSocketController {

    private final SimpMessagingTemplate simpMessagingTemplate;

    /**
     * 客户端通过/app/hello发送websocket消息，
     * 默认转发到/topic/hello给客户端订阅,
     * SendTo 重定向到 /topic/greetings，广播消息
     */
    @MessageMapping("/hello")
    @SendTo("/topic/greetings")
    public Greeting greeting(HelloMessage message) throws Exception {
        Thread.sleep(1000);
        return new Greeting("Hello, " + HtmlUtils.htmlEscape(message.getName()) + "!");
    }

    /**
     * HTTP请求后通过template发送广播消息
     */
    @PostMapping("/hello2/{groupId}")
    public void group(HelloMessage message, @PathVariable("groupId") String groupId) throws Exception {
        Thread.sleep(1000);
        simpMessagingTemplate.convertAndSend("/topic/group/" + groupId,
                new Greeting("Hello Group, " + HtmlUtils.htmlEscape(message.getName()) + "!"));
    }

    /**
     * 客户端通过/app/me发送websocket消息，
     * SendToUser重定向到/user/{username}/queue/me，UserDestinationMessageHandler再次处理发送到/queue/me-user{sessionId}
     * 只有发送方自己能收到这个消息
     * 发送方订阅 /user/queue/me，UserDestinationMessageHandler处理成订阅/queue/me-user{sessionId}
     * 保证只有自己收到
     */
    @MessageMapping("/me")
    @SendToUser("/queue/me")  // 谁请求的发给谁，不是广播
    public Greeting me(HelloMessage message) throws Exception {
        Thread.sleep(1000);
        return new Greeting("Hello MyUser, " + HtmlUtils.htmlEscape(message.getName()) + "!");
    }

    /**
     * 发送消息给指定用户，用户端需要订阅 /user/queue/chat，UserDestinationMessageHandler处理成订阅/queue/chat-user{sessionId}
     */
    @MessageMapping("/chat/{sendTo}")
    public void chat(@DestinationVariable("sendTo") String sendTo, HelloMessage message) throws Exception {
        // 这个消息会发送到 /user/{sendTo}/queue/chat
        // UserDestinationMessageHandler再次处理发送到/queue/chat-user{sessionId}
        // 注意用户订阅前要处理已经登录状态
        simpMessagingTemplate.convertAndSendToUser(sendTo,"/queue/chat",
                new Greeting("Hello " + HtmlUtils.htmlEscape(sendTo) + "! From "+ HtmlUtils.htmlEscape(message.getName())));
    }
}
```

具体可以参考macula-example/macula-example-consumer项目



## 依赖引入

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-websocket</artifactId>
    </dependency>

    <dependency>
        <groupId>cn.hutool</groupId>
        <artifactId>hutool-all</artifactId>
    </dependency>

    <dependency>
        <groupId>dev.macula.boot</groupId>
        <artifactId>macula-boot-starter-redis</artifactId>
    </dependency>

    <dependency>
        <groupId>dev.macula.boot</groupId>
        <artifactId>macula-boot-starter-security</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-messaging</artifactId>
    </dependency>
</dependencies>
```



## 版权说明

- Spring-boot: https://github.com/spring-projects/spring-boot/blob/main/LICENSE.txt
- Spring-framework: https://github.com/spring-projects/spring-framework/blob/main/LICENSE.txt
- Spring-security：https://github.com/spring-projects/spring-security/blob/main/LICENSE.txt
- Hutool：https://github.com/dromara/hutool/blob/v5-master/LICENSE
