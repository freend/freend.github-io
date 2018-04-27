---
layout: post
title: Node ORM Sequelize
tags: [node, sequelize]
---
## Node Sequelize
### sequelize를 알게 된 이유.
node로 사이트를 구축하던 중 mongoDB는 mongoose라는 odm이 모델의 역활을 수행하고 그것으로 많은 사이트가 만들어지고 있단 걸 알게 되었다.<br>
mysql과 node를 사용해야 하는 경우가 생겼다. 처음에는 그냥 쿼리를 적었다(구글에서 node + mysql로 조회하면 대부분 그냥 쿼리를 적는다)<br>
리뷰중에 쿼리 작성으로 사용하면 악용의 소지가 있다고 해서 인터넷을 뒤져보던 중 node orm인 Sequelize를 알게 되었고 그것을 공부하기 시작했다.

### ORM, ODM
MySQL is an example of a relational database - you would use an ORM to translate between your objects in code and the relational representation of the data.<br>
MongoDB is an example of a document database - you would use an ODM to translate between your objects in code and the document representation of the data (if needed).
[영문참조](https://stackoverflow.com/questions/12261866/what-is-the-difference-between-an-orm-and-an-odm)<br>
설명이 틀릴수 있으나 간단히 말하면 RDBMS DB(Mysql, MariaDB 등등)은 ORM이라고 통칭하고 mongoDB와 같은 nosql은 ODM으로 불리는 것으로 보인다.<br>
우선은 간단히 데이터의 형식을 규정하고 확인하는 과정이라고만 생각하자.<br>
또한 sequlieze는 jpa의 기능도 가지고 있다. 지금은 나도 머리가 복잡하니 jpa의 기능은 우선 무시하도록 하겠다.

여기서는 내가 이해하는 방식으로 sequelize에 대해 설치와 사용을 적어보도록 하겠다.

### Install Sequelize
[npm sequelize](https://www.npmjs.com/package/sequelize)에 가면 설치방법을 알 수 있다.<br>
설치까지는 누구든 할 수 있으니 그 다음으로 넘어가 보자.

### Models
models 폴더는 테이블, 컬럼, data type을 선언해주는 기능을 가지고 있으며 데이터의 입,출 기능의 기준이 되는 폴더이다.<br>
폴더를 만들고 그 안에 sysCode.js를 만들었다. <br>
참고로 선언만 재대로 되고 내용이 없어도 테이블 생성 및 id, createdAt, updatedAt 컬럼을 자동으로 생성한다.<br>
최소한의 내용은 다음과 같다.<br>
"`module.exports = function (sequelize, DataTypes) {
    var sample = sequelize.define('sample');
    return sample;
}`"
자 이상태로 모델을 호출하기 위해서 app.js에 다음의 내용을 추가하였다.
"`//sequelize setting
var models = require('./models'); //추가한 부분.`"
이러고 실행을 하면 다음과 같은 오류가 생성된다.
required('folder-name')으로 폴더를 호출하면
folder-name not defined 에러가 발생한다.<br>
이것은 sequelize의 git sample을 보더라도 나타난다. 이것의 이유를 몰라 한동안 끙끙되었는데.
결론적으로 models안에 index.js를 넣으면 오류메세지가 사라진다. 폴더의 모든 모델을 가져올땐 index.js에서<br>
각 파일을 불러와줘야 한다. 그럼 [index.js](https://github.com/freend/node-sequelize/blob/master/models/index.js)를 보도록 하자<br>
여기는 models폴더의 모든파일(하지만 index.js는 제외)을 불러와서 "`db[model.name] = model`"로 각 모델을 db배열에 넣어준다.<br>
그리고 그 다음 내용을 보면 다음과 같은 부분이 있다.<br>
"`Object.keys(db).forEach(modelName => {
    if(db[modelName].associate) {
        db[modelName].associate(db);
    }
});`"
이 부분이 왜 필요한가 하고 뒤져보던 중 다음과 같은 내용을 찾게 되었다.<br>
"Sequelize has changed how associations and instance methods are defined, post version > 4."<br>
대충 해석해 보면 버전 4이상에서는 associations instance metods의 사용법이 변경되었다고 하는거 같다.<br>
저걸 사용해 보기 위해서 3의 사용법으로 나오던 부분인 다음 예시를 응용해서 적용해 보았다.
"`Task.associate = function (models) {
    models.Task.belongsTo(models.User, {
      onDelete: "CASCADE",
      foreignKey: {
        allowNull: false
      }
    });
};`"<br>
해당 부분은 외래키 등을 사용하기 위해 설정하는 부분이다. 해당 부분을 적용하니 그냥 애러를 발생시켰고 공식문서에서
.associate으로 검색해보니 사라진듯 했다.<br>
여기서 jpa를 사용하던 사람들이 말하던 기능이 늘수록 배워야 할 내용이 늘어난다는 말의 의미를 알게되었다.<br>
우선 해당 기능까지의 공부는 추후에 하기로 했다. 우선은 sysCode.js 파일을 분석해 보기로 하자.<br>

이러면 다음과 같은 내용이 나오면서 테이블이 생성됨을 알 수 있다.<br>
Executing (default): CREATE TABLE IF NOT EXISTS `samples` (`id` INTEGER NOT NULL auto_increment , `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL, PRIMARY KEY (`id`)) ENGINE=InnoDB;
Executing (default): SHOW INDEX FROM `samples`<br>

### sequelize sample repository
사실 하단의 레포지토리에 가면 model.md나 route.md를 참조하면 내용을 대충 알수는 있다.
[git repository](https://github.com/freend/node-sequelize)
[sequelize git](https://github.com/sequelize/express-example)에 가면 sequelize에서 만든 예제소스가 있다.<br>
그런데 여기의 package.json을 보면 sequelize의 버전을 알 수 있는데 여기 표기된 버전은 "sequelize": "^3.23.6", 바로 3버전대를 사용한다.<br>
하지만 내가 설치한 아마도 저보다 늦은 분들은 버전이 "sequelize": "^4.37.4"와 같이 최소 4버전을 사용한다.<br>
그래서 sample에서 사용된게 버전4에서는 바뀐게 좀 있다. [공식문서](http://docs.sequelizejs.com/manual/installation/getting-started.html)를 봐도 4에서 3과 변경된 부분이 좀 있어 보인다. 그런데 git에는 적용을 하지 않은 것처럼 보인다.<br>
그래서 sequelize git을 기준으로 하되 바뀐것이나 그런건 4의 문서를 참조해서 작업했다.
