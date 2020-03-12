# JSL Library for Python
JSL은 [WebSocket](https://ko.wikipedia.org/wiki/%EC%9B%B9%EC%86%8C%EC%BC%93) 암호화 통신 프로토콜입니다.
SSL 작동 원리에 기반하여 RSA, AES 암호화 알고리즘을 통해 SSL 인증서 없이도 안전한 통신을 할 수 있도록 해줍니다.

현재 지원되는 서버 언어로는 [Python](python.org), 클라이언트 언어로는 [Python](python.org)과 [JavaScript](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8)가 있습니다.

해당 프로젝트는 JSL Client를 Python3에서 구축하도록 하는 라이브러리입니다.

JSL Server는 Python으로 구현할 수 있습니다. [여기](https://github.com/2runo/JSL-py)에서 JSL Server를 구축하는 방법을 알아보세요. 쉽습니다!

# JSL 작동 방식
JSL은 다음과 같은 순서로 작동합니다.
### 핸드셰이킹
1. server hello - 서버가 클라이언트에게 JSL 프로토콜 사용 여부를 묻습니다.
2. client hello - 클라이언트가 이에 응답합니다.
3. RSA public key 전달 - 서버가 클라이언트에게 RSA public key를 전달합니다.
4. AES key 생성 - 클라이언트에서 랜덤하게 AES key를 생성합니다.
5. AES key 전달 - 클라이언트가 서버에게 AES key를 RSA public key로 암호화하여 전달합니다. 이때 제 3자(해커)는 AES key를 알아낼 수 없습니다.
6. 핸드셰이킹 종료 - 이제 핸드셰이킹이 완료되었습니다!
### 통신
클라이언트가 서버에게 전달한 AES key를 통해 통신 메시지를 암호화하고 해독합니다.

통신 내용은 모두 암호화되므로 제 3자(해커)는 통신 내용을 확인할 수 없습니다!

# 사용법

### 라이브러리 설치
```
pip install pyJSLclient
```

### Echo Server 예제
```python3
from pyJSLclient import *

class simpleClient(JSLclient):
    def onopen(self):
        print('open')
        self.send("hello world")  # 서버에 "hello world" 메시지 전송

    def onmsg(self, msg):
        print('msg:', msg)

    def onclose(self):
        print('closed')


# 서버에 연결
serve("ws://localhost:8000", simpleEcho)
```

### 설명
`onconn()`은 서버와 연결되었을 때 실행됩니다.
```python3
def onconn(self):
    print("connected")
```
`onopen()`은 서버와의 JSL 핸드셰이킹이 성공했을 때 실행됩니다.
```python3
def onopen(self):
    print("handshaking was succeeded")
```
`onmsg(msg)`는 서버로부터 메시지를 받았을 때 실행되며, `msg` 인자를 통해 메시지 내용이 전달됩니다.
```python3
def onmsg(self, msg):
    print("client said:", msg)
```
`onclose()`는 서버와의 연결이 끊어졌을 때 실행됩니다.
```python3
def onclose(self):
    print("closed")
```
`self.send(msg)` 명령어로 서버에게 메시지를 보낼 수 있습니다. 물론 자동으로 암호화됩니다!
