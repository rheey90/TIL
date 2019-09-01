# npm & package.json

### npm

npm이란 재사용 가능한 코드 모듈과 페키지를 접근할 수 페키지 메니저로써 사용자가 프로젝트에 필요한 모듈 혹은 페키지를 npm을 통하여 프로젝트에 적용할 수 있게끔 도와준다. 다른 프로젝트에서 쓴 코드를 복사하고 현재 진행하고 있는 프로젝트에 붙여 재사용할 수 있겠지만 유지 보수 하는데 있어 비효율적이므로 npm을 적극 사용하여 필요한 것들을 받는 것이 좋다.

### package.json

package.json은 프로젝트에 대한 메타 데이터를 담고 있는 파일이다. 프로젝트에 필요한 dependcy들을 관리하고 빌드, 테스트 등과 같은 프로젝트에 필요한 작업들을 돌리는 스크립트를 포함한다.

package.json이 담고 있는 식별 메타 데이터에는 프로젝트 명, 목적, 버전 등과 같은 정보를 포함하고 있다. npm repository에서 프로젝트를 찾기 위해 이는 반드시 이름과 버전 프로퍼티를 담고 있어야 한다.

또한 식별 메타 데이터에는 프로젝트의 라이센스, 설명, 키워드 등도 포함할 수 있다.

함수 메타 데이터에는 npm이 실행 및 빌드에 필요한 프로퍼티들을 포함하고 있다. main이란 프로퍼티는 엡의 시작점으로, 해당 앱을 실행할 때 필요한 파일을 포함한다. repository는 앱이나 모듈의 source가 저장되어 있는 정보를 지닌다. script는 key-value 형식으로 저장되어 있는 key에는 스크립트가 실행한 task등을 명시하고 value는 task와 관련있는 명령어를 포함한다. dependencies도 역시 key-value 형식으로 되어 있으며 프로젝트에 필요한 모듈들이 정의 되어있다. 마지막으로 devDependcies는 빌드, 코드 테스트, 코드 배포 등과 관련된 모듈들을 지니고 있다.

    // package.json 예시
    {
      "name": "npm-example",
      "version": "1.0.0",
      "license": "MIT",
      "description": "This project describes the package.json file in detail and explains what goes inside it.",
      "keywords": ["npm", "package.json", "node.js"],
      "main": "index.js",
      "repository": {
        "type": "git",
        "url": "https://github.com/skyshader/example.git"
      },
      "scripts": {
        "build": "...",
        "test": "..."
      },
      "dependencies": {
        
      },
      "devDependencies": {
        
      }
    }