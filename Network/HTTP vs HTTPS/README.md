# HTTP와 HTTPS의 차이점

HTTP와 HTTPS는 웹 서버와 웹 브라우저 간에 정보를 전송하는 프로토콜이다.  
두 프로토콜의 주요 차이점은 **데이터가 전송되는 방식, 특히 그 데이터가 어떻게 보호되는지**에 있다.

## HTTP (HyperText Transfer Protocol)

HTTP는 웹 서버와 클라이언트(웹 브라우저) 간에 웹 페이지와 그와 관련된 정보를 전송하는 데 사용되는 프로토콜이다. 하지만 HTTP를 사용하면 데이터는 암호화되지 않고 텍스트 형태로 그대로 전송된다. 이 때문에 중간에서 데이터를 가로챌 수 있는 "중간자 공격(Man-In-The-Middle Attack)"에 취약해진다.

## HTTPS (HyperText Transfer Protocol Secure)

반면에 HTTPS는 웹 서버와 클라이언트 간의 모든 통신을 암호화하는 SSL(Secure Sockets Layer) 또는 TLS(Transport Layer Security) 프로토콜을 사용한다. 이를 통해 중간자 공격을 방지하며, 사용자가 민감한 정보(예: 신용카드 번호)를 웹사이트에 제공할 때 이를 보호한다.

### 대칭키와 공개키 암호화

암호화에 사용되는 주요한 두 가지 유형의 키는 대칭키와 공개키다.

**대칭키 암호화**  
이 방식은 암호화와 복호화에 동일한 키를 사용한다. 이 키를 가진 사람은 정보를 암호화하거나 복호화할 수 있다. 이 방식은 효율적이지만, 키를 안전하게 전달하는 것이 중요한 문제가 된다.

**공개키 암호화**  
이 방식은 암호화와 복호화에 서로 다른 두 개의 키(공개키와 개인키)를 사용한다. 공개키는 누구에게나 제공될 수 있으며, 이를 사용하여 데이터를 암호화할 수 있다. 그러나 이 암호화된 데이터를 복호화하려면 해당 개인키가 필요하다. 이 방식은 대칭키 방식보다 안전하지만, 처리 속도가 느리다.

- 암호화 - 공개 키, 복호화 - 개인 키 : 진짜 데이터를 암호화하여 보호하기 위한 목적이다.
 
- 암호화 - 개인 키, 복호화 - 공개 키 : 인증을 위한 목적이다. 즉, 서버에서 개인 키로 데이터를 암호화해서 보냈고 클라이언트에서 공개 키로 복호화가 된다면 해당 서버는 클라이언트 입장에서 신뢰할 수 있다고 판단할 수 있다.


### SSL Handshake

HTTPS 연결이 이루어지는 과정, 즉 SSL 핸드셰이크는 다음과 같다.
![image](https://github.com/minsu20/CS_Study/assets/86006389/ca9f17ff-4bd0-417d-a9af-9506913b2165)
파란색 칸과 노란색 칸은 네트워크 상에서 전달되는 IP 패킷을 표현한 것이다. 파란색 칸에 해당하는 SYN, SYN ACK, ACK는 TCP 레이어의 3-way handshake로, HTTPS가 TCP 기반의 프로토콜이므로 SSL Handshke에 앞서 연결을 생성하기 위해 실시하는 과정이다. 노란색 칸에 해당하는 패킷들이 SSL Handshake라고 보면 된다.

1. **서버가 CA에 인증서 요청**
   - 웹 서버는 자신의 정보와 공개키를 인증 기관(CA)에게 전달한다. 이 정보를 바탕으로 CA는 디지털 인증서를 생성하고, 이를 웹 서버에게 반환한다.

2. **ClientHello**
   - 웹 브라우저(클라이언트)가 웹 사이트에 접속을 요청하면, 서버에 '안녕'이라는 메시지를 보낸다. 이 메시지에는 클라이언트가 지원하는 암호화 알고리즘 목록이 포함되어 있다.

3. **ServerHello**
   - 웹 서버는 브라우저로부터 받은 메시지를 확인하고, 지원 가능한 암호화 알고리즘 중 하나를 선택하여 브라우저에게 알린다.

4. **Certificate / Server Key Exchange (생략 가능) / ServerHello Done**
   - 서버는 클라이언트에게 자신의 디지털 인증서를 전송한다. 이 인증서에는 서버의 **공개키**가 포함되어 있다.
   - 공개 키가 인증서 내에 없는 경우에는, 서버가 직접 공개 키를 클라이언트에게 전달한다. 만약 공개 키가 이미 인증서 내에 있으면 이 단계는 생략될 수 있다.
   - 그리고 서버가 행동을 마쳤음을 전달한다.

5. **Client Key Exchange**
   - 클라이언트는 이제 통신에 사용할 **대칭키**를 생성하고, 이를 서버의 **공개키**로 암호화하여 서버에게 전송한다. 여기서 전달된 대칭키가 SSL Handshake의 목적이자 가장 중요한 수단인 데이터를 실제로 암호화할 대칭키로, 서버는 자신의 **개인키**를 이용해 이를 복호화하여 대칭키를 얻는다.

6. **ChangeCipherSpec / Finished**
   - ChangeCipherSpec 패킷은 클라이언트와 서버 모두가 서로에게 보내는 패킷으로, 교환할 정보를 모두 교환한 뒤 통신할 준비가 다 되었음을 알리는 패킷이다.
   - 그리고 Finished 패킷을 보내어 SSL Handshake를 종료하게 된다.


-> 이렇게 얻은 대칭 키를 활용하여 서로가 서로의 데이터를 안전하게 복호화 하면서 통신할 수 있다. 
