# node.js로 간단한 서버 구축하기

### node.js란

node.js는 구글 크롬의 V8 엔진에 기반을 두어 만든 서버 사이드 플렛폼이다.

- 비동기 I/O 처리 / 이벤트 위주: Node.js 라이브러리의 모든 API는 비동기, 즉 non-blocking 방식으로 작동함으로써 데이터가 완전히 반환될 때까지 기다리지 않고 바로 다음 코드 혹은 API가 실행된다.
- 단일 쓰레드: 이벤트 루프와 함께 단일 쓰레드 모델을 사용한다. 이벤트 메커니즘은 서버가 멈추지 않고 반응하도록 해주어 서버의 확장성을 키워준다.
- 버퍼링의 부재: 데이터 버퍼링이 없고 데이터를 chunk로 출력한다.

### 서버 구축

node.js를 사용하여 서버를 구축하기 위해서 http 모듈을 사용해야 한다. HTTP는 Hyper Text Transfer Protocol의 약자로 인터넷에서 데이터를 주고 받을 수 있는 프로토콜이다. 이는 TCP/IP 기반으로 하여 클라이언트와 서버로부터 요청과 응답을 전송한다. 모든 웹 사이트는 이 프로토콜에 맞춰 개발되어 정보 교환을 가능캐 한다.

    const http = require('http');
    const PORT = 3000;
    const ip = 'localhost';

PORT는 HTTP와 같은 TCP/IP 프로토콜 연결을 받을 수 있는 역할을 수행하며 네트워크의 길 번호라고 볼 수 있으며 localhost는 자기 자신의 네트워크 이름을 말한다. 위와 같은 URL은 [http://localhost:3000이](http://localhost:3000이) 된다. 아래는 클라이언트 측에서 문자열을 보내면 서버에서 문자열을 소문자화 혹은 대문자화를 하여 응답해주는 로컬 사이트다.

    const server = http.createServer((request, response) => {
    	let headers = defaultCorsheader;
    	
    	if (request.method === 'OPTIONS') {
        response.writeHead(200, headers);
        response.end();
      }
      if (request.method === 'POST') {
        let body = [];
        if (request.url === '/upper') {
          request.on('data', chunk => {
            body.push(chunk);
          });
          request.on('end', () => {
            body = Buffer.concat(body).toString().toUpperCase();
            response.writeHead(200, headers);
            response.end(JSON.stringify(body));
          })
        } else if (request.url === '/lower') {
          request.on('data', chunk => {
            body.push(chunk);
          });
          request.on('end', () => {
            body = Buffer.concat(body).toString().toLowerCase();
            response.writeHead(200, headers);
            response.end(JSON.stringify(body));
          })
        } 
      } else {
        response.writeHead(400);
        response.end();
      }
      console.log(
        `http request method is ${request.method}, url is ${request.url}`
      );
    });
    
    server.listen(PORT, ip, () => {
      console.log(`http server listen on ${ip}:${PORT}`);
    });
    
    const defaultCorsHeader = {
      'access-control-allow-origin': '*',
      'access-control-allow-methods': 'GET, POST, PUT, DELETE, OPTIONS',
      'access-control-allow-headers': 'content-type, accept',
      'access-control-max-age': 10
    };

서버는 http.createServer라는 함수를 사용하여 구축할 수 있으며 request와 response라는 인자를 받는다. request는 요청, 즉 클라이언트 측에서 서버에게 어느 특정한 요청을 보내면 서버는 response, 즉 응답을 통해 클라이언트가 요청한 내용을 처리한다. 주로 클라이언트 쪽에서 보내는 요청은 OPTIONS, GET, POST 등이 있다. OPTIONS는 요청한 리소스에 대하여 서버와 어떤 의사소통을 할 수 있는 옵션들이다. GET은 클라이언트 측에서 서버로부터 얻고자 하는 리소스 요청으로써 응답을 JSON 형태로 응답한다. POST는 클라이언트에서 서버로 데이터 송신을 요청할 때 쓰인다. 위에서도 언급한 바와 같이 node.js는 데이터를 chunk 형태로 보내줌으로 chunk를 받고 이를 가공시켜 서버로 보내줘야 하는데 이 과정에서 JSON 형태로 주로 보내준다. 그러므로 JSON.parse과 JSON.stringify를 사용하여 보내준다. 그리고 request.url이 어떻게 되는지에 따라서 데이터를 가공하는 과정이 살짝 다르다는 것을 볼 수 있다. 이 외에도 응답을 보내줄 때 header에 내용을 담기 위하여 status code, CORS에 관한 내용을 보내준다. Status code는 HTTP 요청이 성공적으로 완료되었는지 알려주는 3자리 코드이다. 다른 도메인의 서버의 URL을 호출하여 데이터를 가져와야 하는 경우, 보안 문제가 발생할 수 있는데 이를 해결하기 위해서 CORS (Cross-Origin Resource Sharing)를 사용하여 브라우저와 서버 간의 cross-origin 요청을 허용 여부를 안전하게 결정하도록 상호작용할 수 있는 방법이다. CORS에 필요한 내용들을 header란 변수에 담아주어 status code와 함께 header를 쓸 수 있다. 마지막으로, server.listen은 연결에 대하여 '듣는 것'을 실행하기 위해 사용된다.