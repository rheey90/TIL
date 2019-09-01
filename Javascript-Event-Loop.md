# Javascript Event Loop

브라우저는 single-thread이며 event-driven 방식으로 동작한다. Single-thread라면 thread가 하나이므로 하나의 작업만 처리할 수 있다. 그러나, 브라우저의 작동 방식을 보면 여러 작업들이 동시에 처리되는 것처럼 보이는데 event loop이 이를 가능캐한다.

![](https://poiemaweb.com/img/event-loop.png)

구글의 V8 엔진을 포함한 대부분의 자바스크립트의 엔진은 2개의 영역으로 나뉜다. Call stack은 작업이 요청되면 요청된 작업은 순차적으로 쌓이고 실행된다. 단 하나의 call stack 밖에 없기 때문에 한 작업이 종료되기 전까지 다른 작업이 실행될 수 없다. Heap은 동적으로 생성된 객체 인스턴스가 할당되는 영역을 말한다. 동시에 진행되는 작업을 지원하기 위해 필요한 비동기 요청 처리는 브라우저나 node.js가 담당한다. Event queue는 비동기 처리 함수의 콜백 함수, 비동기식 이벤트 핸들러, timer 함수의 콜백 함수가 보관되는 영역으로 event loop에 의해 call stack이 비워졌을 때 순차적으로 call stack으로 이동되어 실행된다. Event loop는 call stack 내에서 현재 실행 중이 작업이 있는 그리고 event queue에 작업이 있는지 계속 확인하여 call stack이 비어있을 시, event queue 내의 작업이 call stack으로 이동하고 실행된다.