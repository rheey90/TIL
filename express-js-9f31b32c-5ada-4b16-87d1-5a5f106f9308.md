# express.js

express.js는 node.js 상에서 동작하는 웹 개발 프레임워크로써 가볍고 유연하게 웹 프레임워크를 구성할 수 있다. 이를 가능하도록 만들어주는 것이 미들웨어 구조인데 미들웨어는 개발자가 필요한 것만 선택하여 express와 결합해 사용할 수 있도록 해준다.

    const http = require('http');
    const requestHandler = require('./request-handler');
    const port = 3000;
    const ip = 'localhost';
    
    const server = http.createServer(requestHandler);
    server.listen(port, ip);
    
    module.exports = server;

    let messages = {
      results: []
    };
    
    const requestHandler = function(request, response) {
     
      const statusCode = 200;
    
      const headers = defaultCorsHeaders;
    
      headers['Content-Type'] = 'application/json';
      
      if (request.method === 'OPTIONS') {
        response.writeHead(statusCode, headers);
        response.end();
    
      } else if (request.method === 'GET' && request.url === '/classes/messages') {
        response.writeHead(statusCode, headers);
        response.end(JSON.stringify(messages));
    
      } else if (request.method === 'POST' && request.url === '/classes/messages') {
        let body = [];
    
        request.on('data', (chunk) => {
          body.push(chunk);
        });
        request.on('end', () => {
          body = Buffer.concat(body).toString();
          messages.results.push(JSON.parse(body));
          response.writeHead(201, headers);
          response.end(JSON.stringify(messages));
    
        });
      } else if (request.url !== '/classes/messages') {
        response.writeHead(404, headers);
        response.end();
      }
    }
    
    const defaultCorsHeaders = {
      'access-control-allow-origin': '*',
      'access-control-allow-methods': 'GET, POST, PUT, DELETE, OPTIONS',
      'access-control-allow-headers': 'content-type, accept',
      'access-control-max-age': 10 
    }
    module.exports = requestHandler;

위는 chatterbox 클라이언트를 구현하고 이를 위한 서버를 개발하기 위하여 작성된 코드이다. 각 클라이언트 측 요청을 처리하는 것을 볼 수 있다. 이제 아래는 express로 리팩토링하여 똑같은 기능을 수행하는 코드이다.

    const express = require('express');
    const app = express();
    const cors = require('cors');
    const bodyParser = require('body-parser');
    const port = 3000;
    
    const defaultCorsHeaders = {
        'access-control-allow-origin': '*',
        'access-control-allow-methods': 'GET, POST, PUT, DELETE, OPTIONS',
        'access-control-allow-headers': 'content-type, accept',
        'access-control-max-age': 10
    }
    const headers = defaultCorsHeaders;
    headers['content-type'] = 'application/json';
    
    const statusCode = 200;
    
    app.use(cors(headers));
    app.use(bodyParser.urlencoded({extended: false}));
    app.use(bodyParser.json());
    
    
    let messages = {
        results: []
    };
    
    app.get('/classes/messages', (req, res) => {
        res.status(statusCode);
        res.setHeader("Content-Type", "application/json");
        res.send(JSON.stringify(messages));
    });
    
    app.post('/classes/messages', (req, res) => {
        res.status(201);
        messages.results.push(req.body);
        res.setHeader("Content-Type", "application/json");
        res.send(JSON.stringify(messages));
    });
    
    app.listen(port);
    
    module.exports = app;

express로 리팩토링 했을 때 코드가 훨씬 더 간결해진 것을 볼 수 있다. 부분적으로 이는 미들웨어를 사용했기에 가능한 것이다. cors 모듈을 사용하여 바로 CORS 문제를 해결하고 POST 요청이 왔을 시, bodyParser를 사용하여 chuck를 이어주고 JSON.parse 해주는 코드를 생략할 수 있다. 그리고 request.method를 사용하는 대신 간단하게 .get, .post 메소드를 사용해주면 되고 request.url은 바로 url를 문자열로 인자로 주면 된다. express.js는 코드를 더 간결하고 직관적으로 바꿔줌으로써 가독성이 높아지고 코드를 이해하기 쉽게 도와준다.