---
layout: post
title: Spring에서 웹소켓을 이용한 채팅
---

##### 웹소켓이란 ?

- <http://d2.naver.com/helloworld/1336>
- <http://www.joinc.co.kr/w/man/12/websocket>

##### POM.xml에 다음을 추가한다.
```xml
<dependency>
    <groupId>javax</groupId>
    <artifactId>javaee-api</artifactId>
    <version>7.0</version>
</dependency>
```

##### Broadsocket.java 생성
```java
import java.io.IOException;
import java.util.Collections;
import java.util.HashSet;
import java.util.Set;

import javax.websocket.OnClose;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.ServerEndpoint;

@ServerEndpoint("/broadcasting")
public class Broadsocket {

    private static Set<Session> clients = Collections
            .synchronizedSet(new HashSet<Session>());

    @OnMessage
    public void onMessage(String message, Session session) throws IOException {
        System.out.println(message);
        synchronized (clients) {
            // Iterate over the connected sessions
            // and broadcast the received message
            for (Session client : clients) {
                if (!client.equals(session)) {
                    client.getBasicRemote().sendText(message);
                }
            }
        }
    }

    @OnOpen
    public void onOpen(Session session) {
        // Add session to the connected sessions set
        System.out.println(session);
        clients.add(session);
    }

    @OnClose
    public void onClose(Session session) {
        // Remove session from the connected sessions set
        clients.remove(session);
    }
}
```

##### broadcast.jsp 생성
```markup
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Testing websockets</title>
</head>
<body>
    <fieldset>
        <textarea id="messageWindow" rows="10" cols="50" readonly="true"></textarea>
        <br/>
        <input id="inputMessage" type="text"/>
        <input type="submit" value="send" onclick="send()" />
    </fieldset>
</body>
    <script type="text/javascript">
        var textarea = document.getElementById("messageWindow");
        var webSocket = new WebSocket('ws://localhost:8080/프로젝트명/broadcasting');
        var inputMessage = document.getElementById('inputMessage');
    webSocket.onerror = function(event) {
      onError(event)
    };
    webSocket.onopen = function(event) {
      onOpen(event)
    };
    webSocket.onmessage = function(event) {
      onMessage(event)
    };
    function onMessage(event) {
        textarea.value += "상대 : " + event.data + "\n";
    }
    function onOpen(event) {
        textarea.value += "연결 성공\n";
    }
    function onError(event) {
      alert(event.data);
    }
    function send() {
        textarea.value += "나 : " + inputMessage.value + "\n";
        webSocket.send(inputMessage.value);
        inputMessage.value = "";
    }
  </script>
</html>
```

##### 참고

- <http://stackoverflow.com/questions/21038100/getting-errors-in-websocket-server-endpoint>
- <http://choiyb2.tistory.com/81>
