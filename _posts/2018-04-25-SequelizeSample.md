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


### sequelize sample repository
사실 하단의 레포지토리에 가면 model.md나 route.md를 참조하면 내용을 대충 알수는 있다.
[git repository](https://github.com/freend/node-sequelize)
[sequelize git](https://github.com/sequelize/express-example)에 가면 sequelize에서 만든 예제소스가 있다.<br>
그런데 여기의 package.json을 보면 sequelize의 버전을 알 수 있는데 여기 표기된 버전은 "sequelize": "^3.23.6", 바로 3버전대를 사용한다.<br>
하지만 내가 설치한 아마도 저보다 늦은 분들은 버전이 "sequelize": "^4.37.4"와 같이 최소 4버전을 사용한다.<br>
그래서 sample에서 사용된게 버전4에서는 바뀐게 좀 있다. [공식문서](http://docs.sequelizejs.com/manual/installation/getting-started.html)를 봐도 4에서 3과 변경된 부분이 좀 있어 보인다.<br>
그래서 sequelize git을 기준으로 하되 바뀐것이나 그런건 4의 문서를 참조해서 작업했다.
