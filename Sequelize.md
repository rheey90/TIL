# Sequelize

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