# Tech used in two-week-project

## Sequelize

### Sequelize란

Sequelize는 Node.js에서 사용할 수 있는 promise 기반 ORM이다. ORM은 Object-Relational-Mapping의 약자로서 객체를 관계형 데이터베이스에 매핑해 데이터베이스의 기능들을 추상적으로 사용할 수 있게 해준다. ORM은 데이터베이스 쿼리를 추상화하여 본래 기능을 구현하는 로직에 집중할 수 있게 해주고 특정 데이터베이스에 대한 종속성이 사라져 데이터베이스가 바뀌는 상황에도 유현하게 대처할 수 있으며 데이터베이스 마이그레션을 쉽게 할 수 있다는 장점들을 지니고 있다. Sequelize는 mySQL, Postgres, MariaDB, SQLite, Microsoft SQL Server 등 여러 RDBMS를 지원한다.

일반적으로 데이터베이스로부터 데이터를 쓰고 가져오기 위해서 쿼리문을 사용하고 .sql 파일을 만들어야 하지만 sequelize를 사용하면 데이터베이스에 table로 들어갈 models를 만들고 models에 기반을 두어 데이터베이스에 table을 migrate한다. 그리고 데이터베이스로부터 데이터를 가져오기 위해서 쿼리문이 아닌 데이터베이스에서 직접 찾는다는 의미로 find를 사용하여 조건에 맞는 데이터를 받아 올 수 있다.

### Sequelize 사용하기

```
$ npm install sequelize --save-dev
$ npm install sequelize-cli --save-dev
```

위와 같은 npm 페키지들을 설치하여 sequelize를 사용할 수 있다. Sequelize 패키지는 서버와 데이터베이스를 연결하는 connection과 데이터베이스 table에 넣은 models를 만들 때 사용되고 sequelize-cli는 model에 기반한 데이터베이스에 table을 migrate할 때 쓰인다.

```javascript
const Sequelize = require('sequelize');

const sequelize = new Sequelize('triJournal', 'root', '', {
  host: '127.0.0.1',
  dialect: 'mysql',
  operatorsAliases: false
});

module.exports = sequelize;
global.sequelize = sequelize;
```

서버와 데이터베이스의 연결을 담당하는 connection이다. global.sequelize를 사용하여 전역으로 sequelize를 활용할 수 있게끔 한다.

```
$ npx sequelize init
```

Model들을 만들기 위해 터미널에서 위와 같은 명령어를 입력하면 자동으로 models란 폴더와 index.js 파일이 생성된다. 다음은 데이터베이스의 테이블로 들어갈 model을 만들어준다.

```javascript
const Sequelize = require('sequelize');

module.exports = sequelize.define('Journal', {
  id: {
    type: Sequelize.INTEGER(11),
    allowNull: false,
    autoIncrement: true,
    primaryKey: true
  },
  best: {
    type: Sequelize.STRING(100),
    allowNull: false
  },
  worst: {
    type: Sequelize.STRING(100),
    allowNull: false
  },
  todo: {
    type: Sequelize.STRING(100),
    allowNull: false
  },
  longLog: {
    type: Sequelize.STRING(300),
    allowNull: true
  },
  picUrl: {
    type: Sequelize.STRING(300),
    allowNull: true
  },
  classMethods: {
    associate: function(models) {
      Journal.belongsTo(models.User, {
        foreignKey: 'userId',
        onDelete: 'CASCADE'
      });
    }
  }
});
```

```javascript
const Sequelize = require('sequelize');

module.exports = sequelize.define('User', {
  id: {
    type: Sequelize.INTEGER(11),
    allowNull: false,
    autoIncrement: true,
    primaryKey: true
  },
  username: {
    type: Sequelize.STRING(35),
    allowNull: false,
    unique: true
  },
  email: {
    type: Sequelize.STRING(100),
    allowNull: false,
    unique: true
  },
  password: {
    type: Sequelize.STRING(20),
    allowNull: false
  },
  userProfilePic: {
    type: Sequelize.STRING(1234),
    allowNull: true
  },
  classMethods: {
    associate: function(models) {
      User.hasMany(models.Journal, {
        onDelete: 'CASCADE'
      });
    }
  }
});

```

만일 두 개 이상의 테이블을 만들어야 할 경우, 두 테이블의 관계를 고려하여 association을 형성해줘야 한다.

```
$ npx sequelize migration:create --name <name>
```

위와 같은 명령문을 실행하면 테이블을 옮기기 위한 migration 파일 자동으로 생성된다.

```javascript
'use strict';

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('users', {
      id: {
        type: Sequelize.INTEGER(11),
        allowNull: false,
        autoIncrement: true,
        primaryKey: true
      },
      username: {
        type: Sequelize.STRING(35),
        allowNull: false,
        unique: true
      },
      email: {
        type: Sequelize.STRING(100),
        allowNull: false,
        unique: true
      },
      password: {
        type: Sequelize.STRING(20),
        allowNull: false
      },
      userProfilePic: {
        type: Sequelize.STRING(1234),
        allowNull: true
      },
      createdAt: Sequelize.DATE,
      updatedAt: Sequelize.DATE
    });
  },

  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('users');
  }
};
```

```javascript
'use strict';

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('journals', {
      id: {
        type: Sequelize.INTEGER(11),
        allowNull: false,
        autoIncrement: true,
        primaryKey: true
      },
      best: {
        type: Sequelize.STRING(100),
        allowNull: false
      },
      worst: {
        type: Sequelize.STRING(100),
        allowNull: false
      },
      todo: {
        type: Sequelize.STRING(100),
        allowNull: false
      },
      longLog: {
        type: Sequelize.STRING(300),
        allowNull: true
      },
      picUrl: {
        type: Sequelize.STRING(300),
        allowNull: true
      },
      userId: {
        type: Sequelize.INTEGER(11),
        // onDelete: 'CASCADE',
        references: {
          model: 'users',
          key: 'id'
        }
      },
      createdAt: Sequelize.DATE,
      updatedAt: Sequelize.DATE
    });
  },

  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('journals');
  }
};
```

Migration 파일 같은 경우는 promise 형태이기 때문에 이를 고려하여 return을 써줘야 한다. 그리고 테이블 관계를 고려하여 foreignKey를 설정해줘야 한다. 그리고 createdAt과 updatedAt 키를 만들어줘야 한다. 모든 키와 값들은 models에서 가져온 것들이다.

```
$ npx sequelize db:migrate
```

위와 같은 명령문을 실행하면 table을 데이터베이스로 migrate 된다.



## multer-S3

유저 프로필 사진과 일기 사진을 저장하고 불러오기 위해서 AWS S3 버킷을 사용했다. AWS S3 버킷은 사용자들이 웹 앱이나 서비스를 이용하는데 필수적인 파일들을 선정하여 build란 폴더에 담아 사용자가 한 번에 가져갈 수 있도록 포장을 한다. Build를 배포하기 위해서 아마존에서 제공하는 Amazon S3 클라우드를 사용할 수 있다. Amazon S3는 간단한 웹 서비스 인터페이스를 가지고 있으며 웹 어디에서나 데이터를 저장하고 가져올 수 있는 기능을 제공한다. 그럼 Amazon S3가 어떤 종류의 저장소를 가지고 있는지 그리고 어떤 장점들을 지니고 있을까?

Amazon S3는 일반 목적, 비주기적 접근, 아카이브 등의 세 가지 종류의 저장소를 제공한다. 데이터의 생애주기를 관리하기 위해서 Amazon S3는 수정 가능한 생애주기 정책들을 가지고 있다. Amazon S3 사용자들은 생애주기 정책을 통하여 코드를 수정할 필요없이 데이터를 자동적으로 목적에 알맞는 저장소에 옮길 수 있다. 데이터 접속 권한을 통제하기 위해 Amazon S3는 여러 종류의 접근 권한, 통제, 암호화 옵션들을 제공한다.

Amazon S3의 장점들은 다음과 같다. Amazon S3는 데이터를 담을 버킷을 제공하는데 이의 용량은 거의 무제한이다. 각 객체마다 5TB란 어마어마한 데이터를 저장할 수 있게 해준다. 그리고 저장된 데이터를 수정 및 다운로드 권한을 자유롭게 설정할 수 있고 REST와 SOAP 인터페이스를 사용하기 때문에 어느 인터넷 개발 툴킷과 작업이 용이하다. 그러므로 위에서 언급한 포장 된 build 파일들을 Amazon S3가 제공하는 버킷에 담아 사용자들이 다운로드 받을 수 있다.

multer-S3란 모듈은 node.js에서 AWS S3 버킷에 저장된 파일을 접근하도록 가능하게 해준다.

```
npm install multer multer-s3 aws-sdk // multer-s3 모듈을 설치
```

```javascript
const aws = require('aws-sdk');
const config = require('./config-profilepic');
const multer = require('multer');
const multerS3 = require('multer-s3');

aws.config.update({
    "accessKeyId": " <your access key id> ",
    "secretAccessKey": " <your secret access key> ",
    "region": "us-east-2"
}); // AWS S3 설정

let s3 = new aws.S3(); // AWS S3 인스턴스 생성

let uploadProfilePic = multer({
  storage: multerS3({
    s3: s3,
    bucket: 'trijournal-postimage', // 버킷 이름
    acl: 'public-read',
    metadata: function(req, file, cb) {
      cb(null, { fieldName: 'TESTING_META_DATA' });
    },
    key: function(req, file, cb) {
      cb(null, Date.now().toString());
    }
  })
});

module.exports = uploadProfilePic;
```

모듈을 설치 후, node.js에서 AWS S3에 접근이 가능하도록 S3 버킷 설정을 public read를 전환하고 access key id와 secret access key를 발급 받아야 한다. (발급은 이 사이트를 참고: https://supsystic.com/documentation/id-secret-access-key-amazon-s3/). 그리고 multer-S3의 인스턴스를 만들어 주고 다른 파일에서 접근이 가능하도록 export 시켜준다.

```javascript
// 라우터 설정
const controller = require('./controller');
const router = require('express').Router();
const uploadProfilePic = require('./services/file-upload-profilepic');

router.post('/users/postUserProfilePic', uploadProfilePic.single('image'), (req, res) => {
  return res.json({ imageUrl: req.file.location });
});

module.exports = router;
```

```javascript
// 컨트롤러 설정
var sign = require('../models/queries/sign');
var users = require('../models/queries/users');
var posts = require('../models/queries/posts');

module.exports = {
  users: {
    uploadprofilepic: async function(req,res) {
      try {
        const data = await users.uploadprofilepic(req,res);
        res.status(201);
        res.send(data)
      } catch(err) {
        console.error(err)
      }
    }
  }
};
```

```javascript
// 모델 설정
const express = require('express');
const parser = require('body-parser');
const cors = require('cors');
const User = require('../tables/index').User;
const app = express();

app.use(parser.json());
app.use(parser.urlencoded({ extended: false }));
app.use(cors({ credentials: true, origin: 'http://localhost:3000' }));

app.get('/', (req, res) => {
  res.status(200).send('Success');
});

module.exports = {
  uploadprofilepic: (req, res) => {
    User.Journal.update(
      {
        userProfilePic: req.file.location
      },
      { where: { id: req.params.postId } }
    );
  }
};
```

위와 같이 코드를 작성하여 AWS S3에 유저 사진 업로드를 가능하게 만들었다. req.file.location 같은 경우는 프론트 엔드에서 파일을 읽어오도록 하였다. 그리고 데이터베이스의 파일 저장 위치는 User 테이블에 userProfilePic이다.



## Passport/JWT

유저 로그인 인증은 passport와 JWT를 사용해서 구현했다.

```
npm install passport jsonwebtoken passport-jwt // passport/JWT 모듈 설치
```

```javascript
// app.js 설정
// JWT 설정이 들어간 파일을 import

const express = require('express');
const cors = require('cors');
const morgan = require('morgan');
const parser = require('body-parser');
const router = require('./routes.js');
const app = express();
const port = 3000;
const config = require('./config/jwtConfig');

app.use(cors({ credentials: true, origin: 'http://localhost:3000' }));
app.use(morgan('dev'));
app.use(parser.json());
app.use('/', router);
app.set('jwt-secret', config.secret);

app.set('port', port);

if (!module.parent) {
  app.listen(port);
  console.log('Listening on', port);
}

module.exports.app = app;
```

```javascript
// 라우터 설정

const controller = require('./controller');
const router = require('express').Router();

const auth = require('./middleware/auth');

router.use('/test', auth);

router.post('/sign/signin', controller.sign.signin);

router.post('/users/checkId', controller.users.checkId);
```

```javascript
// 컨트롤러 설정

var sign = require('../models/queries/sign');

module.exports = {
  sign: {
    signin: async function(req, res) {
      try {
        const data = await sign.signin(req, res); //req => req,res
        res.status(200);
        res.send(data);
      } catch (err) {
        console.error(err);
      }
    },
    signout: async function(req, res) {
      try {
        const data = await sign.signout(req, res);
        res.status(201);
        res.send(data);
      } catch (err) {
        console.error(err);
      }
    },
    checkSign: async function(req, res) {
      try {
        const data = await sign.checkSign(req, res);
        res.status(201);
        res.send(data);
      } catch (err) {
        console.log(err);
      }
    }
  }
};
```

```javascript
// 모델 설정

const express = require('express');
const parser = require('body-parser');
const cors = require('cors');
const User = require('../tables/index').User;
const app = express();
const passport = require('passport');
const passportJWT = require('passport-jwt');
const jwt = require('jsonwebtoken');
const auth = require('../../middleware/auth');

const getUser = async obj => {
  return await User.findOne({ where: obj });
};

let ExtractJwt = passportJWT.ExtractJwt;
let JwtStrategy = passportJWT.Strategy;
let jwtOptions = {};

jwtOptions.jwtFromRequest = ExtractJwt.fromAuthHeaderAsBearerToken();
jwtOptions.secretOrKey = ' <your secret key> ';

let strategy = new JwtStrategy(jwtOptions, (jwt_payload, next) => {
  let user = getUser({ id: jwt_payload.id });
  if (user) {
    next(null, user);
  } else {
    next(null, false);
  }
});
passport.use(strategy);

app.use(passport.initialize());
app.use(parser.json());
app.use(parser.urlencoded({ extended: false }));
app.use(cors({ credentials: true, origin: 'http://localhost:3000' }));
app.get('/', (req, res) => {
  res.status(200).send('Success');
});

module.exports = {
  signin: (req, res) => {
    return User.findOne({
      where: {
        username: req.body.username,
        password: req.body.password
      }
    }).then(function(result) {
      if (result) {
        let payload = { id: result.id };
        let token = jwt.sign(payload, jwtOptions.secretOrKey);
        passport.authenticate('jwt', { session: false });
        res.setHeader('x-access-token', token);
        return { isLogIn: true };
      } else {
        return { isLogIn: false };
      }
    });
  },
  signout: (req, res) => {
    let tokenValidation = auth(req, res);
    if (tokenValidation.id) {
      return { isLogIn: false };
    }
  }
};
```

passport는 node.js의 미들웨어로써 다양한 인증 전략을 구성할 수 있는 기능을 제공한다. JWT를 사용하여 암호화된 토큰을 만들어 유저 인증을 가능캐하고 이는 한 객체의 형태로 주어져 이를 활용해 passport에서 제공하는 인증 전략을 짤 수 있다.

유저가 로그인하면 백엔드에서 인증된 토큰을 생성하여 반환하고 클라이언트측에서 토큰을 로컬로 저장하여 인증이 필요할 때마다 토큰을 보낸다. 인증은 미들웨어를 통해 이뤄지고 토큰이 유효할 때만 가능하다.

```javascript
// 토큰 인증 미들웨어

const jwt = require('jsonwebtoken');

const authMiddleware = (req, res) => {
  const token =
    req.headers['x-access-token'] || req.query.token || req.body.token;

  if (!token) {
    return res.status(403).json({
      success: false,
      message: 'you do not have the valid token'
    });
  }

  const p = jwt.verify(token, ' <your secret key> ');

  return p;
};

module.exports = authMiddleware;
```

위와 같이 코드를 사용하여 JWT 인증 방식을 구현하였다. 세션-쿠키 대신에 JWT를 사용한 이유는 JWT는 사용이 간편하며 토큰이 발급된 후 검증만 하면 되기 때문에 세션과는 달리 추가 저장소가 필요없다. 이는 서버를 확장하거나 유지 및 보수하는데 유리하다. 그리고 JWT는 확장성이 뛰어나므로 토큰에 기반을 두고 다른 인증 시스템에 접근이 가능하다.