---
layout: post
title: Nas To Server
tags: [nas]
---

## nas에 홈페이지 올리기
### 개요
원래 사용하던 서비스 모델은 아마존 EC2, RDS를 서버로 사용하고 있었다.
서비스를 중단할 상황에 처하게 되었으나 서비스를 유지할 방법을 찾던 중 다음과 같은 방법으로 서비스를 좀더 이어가도록 하기로 했다.
우리가 서비스를 유지하고 싶어하기도 했고, 유료사용자들을 위해서라도 필요했기 때문이다.

1. aws 서비스를 사용중지하며 웹과 db를 nas로 이동한다.
2. 도메인 포워딩을 통해서 어플을 변경하지 않으면서 서비스에 접속하도록 한다.

위의 두가지를 전제조건으로 다음의 작업을 진행했다.

### synology nas에 웹서버를 구축하기
기종 및 서버 구성은 다음과 같다.
1. nas : synology ds216j
2. shared adapter : iptime A604

#### 서버구성
tomcat7, java8, spring framework4, mybatis
mariadb5 : 원래는 mysql이었으나 synology nas에는 mariadb 설치가 용이해서 이전했다.
mariadb는 5와 10버전이 있으나 10은 mysql과의 호완성이 없다는 정보를 인터넷에서 얻어서 5버전으로 작업했다.

#### 작업과정
1. 처음에 할 일은 nas가 외부에서 접속하게 할 수 있도록 하는 것이다. 이를 위해
제어판 -> 외부억세스 -> DDNS에서 외부접속이 가능하게 하자.
해당 방법은 <a href="https://nas.moe/archives/648" target="_blank">참조사이트</a>를 참조하자.

2. 다음은 제어판 -> 외부억세스 -> 라우터 구성에서 사용을 원하는 포트를 열어주자.
나는 외부에서 tomcat의 접속, mysql의 접속을 위해 8080과 3456포트를 연다고 가정한다.
7070은 nas에 tomcat 설치시 기본 포트이다. 여기서 포트를 바꾼건 나중에 tomcat에서 포트번호를 변경하는 부분까지 작성하기 위해서이다.
라우터 구성에서 생성, 사용자 지정포트 해서 각각 포트를 생성해준다.
연결테스트를 한 결과 재대로 되면 나스가 외부랑 연결된 준비를 마친 것이다.

3. 다음은 라우터에서 포트를 열어줘야 한다. 이것을 열어주지 않으면 외부와의
연결은 영영 막힌 것이다.
위의 주소를 참조하면 된다. 단 내부 IP주소는 본인의 nas가 되어야 한다.
외부포트는 내부포트랑 달라도 상관없다. 단 외부에서 접속할때는 외부포트 번호로 해야 하며
2개를 연 후 내부포트는 위에서 임의로 설정한 8080과 3456포트로 적어주면 된다.
편하게 하려면 두개의 포트를 같게 설정해도 된다.
<a href="http://brand-me.tistory.com/194" target="_blank">참조사이트</a>

4. 패키지 센터로 가서 java8, tomcat7을 설치해주자.
<a href="http://devks.tistory.com/18" target="_blank">참조사이트</a>
만들때 공유 폴더 이름을 물어보는데 나는 그냥 Tomcat이라고 했다 그러면 최상위 공유
폴더로 만들어진다.
다 설치가 끝난후 내부아이피 주소:7070을 치면 에러페이지가 나온다. 뭐 물론 기본
설정이기 때문에 내부아이피 주소:7070/manager/html등등을 뒤에 붙이면 정상적으로 나오는 걸 알 수 있다.
하지만 우리가 만든 war파일이 아니니 다 필요없다
해당 공유폴더의 모든 파일과 폴더를 다 지워주자.

5. 이제 file station을 열면 최상위 폴더에 Tomcat이 보인다 거기다 war를 넣자.
그러고 tomcat을 다시 켜면 되는데 터미널 등을 사용해도 되지만 패키지 센터에서
설치됨을 보면 tomcat7이 보인다 그것을 열어주면 작업이라고 있는데 그것을 눌러서
정지/시작하기를 해도 된다.

이제 내부아이피주소:7070을 하면 톰캣으로 웹서버가 정상적으로 작동함을 알 수 있다.
나같은 경우 aws의 RDS를 데이터베이스로 사용했으므로 서버는 nas에 있어도 RDS의 데이터베이스에
접속해서 작동됨을 확인할 수 있다.
이제 톰캣에서 포트번호를 변경해 보도록 하자.
톰캣의 설정은 /var/packages/Tomcat7/target/src/conf 에 가서 보면
server.xml이라는 파일이 있다.

<Connector port="7070" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
<Connector executor="tomcatThreadPool"
               port="7070" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
이라고되어있는부분의 포트번호를 변경하면 된다. 물론 해당 작업을 한 후 톰캣을 다시 작동시켜야 한다.
6. 이제 외부에서 접속해 보자 제어판의 외부억세스의 DDNS를 다시한번 보자
호스트 이름이 있는데 절대 이거로 접속하면 안된다.
이건 뒤에 포트번호 5000번이 붙어있는 거다.
즉 name.synology.me 는 name.synology.me:5000이랑 동일하다
ddns에 보면 외부주소 라는 ip주소가 있는데 우리가 붙을건 바로 이것이다.
외부주소ip:포트번호 하면 이제 외부에서도 우리의 웹서버에 붙을 수 있는 것이다.

이제 데이터 베이스도 나스로 옮겨볼 것이다.
7. 이제 많이 건너왔다 남은건 데이터베이스의 연결이다.
mariaDB를 설치한다. 그냥 5버전을 설치하자 phpMyadmin을 설치해서 하자는 이야기가 있는데
phpMyadmin을 설치하면 10버전을 설치해버리기 때문이다.
참조사이트
mariaDB 연결
http://elvis-note.tistory.com/entry/Spring-%EA%B0%9C%EB%B0%9C-13-Mybatis-Maria-DB%EC%97%B0%EB%8F%99
mariaDB는 mysql workbench로도 연결이 가능하다.
우선 그전에 root의 비밀번호를 설정하자. 메인메뉴 선택 후 mariaDB 5를 선택하면 패스워드 변경 부분이 있다. 그걸 선택하고 패스워드
재설정을 해서 mariaDB root의 비밀번호를 선정한다.
이제 접속을 해보자. 외부아이피 주소와 포트번호를 맞추고 사용자 root와 비밀번호를 했는데 여기서 갑자기 에러가 발생한다.
여기서 꽤 많이 고전을 했다. 
MySQL, ERROR 1130 (HY000): Host '192.168.0.1' is not allowed to connect to this MySQL Server
이건 또 뭔 소리야.. 한다.
이건 해결하려면 터미널로 접속해야 한다.
<a href="https://redmilk.co.kr/archives/1513" target="_blank">나스에서 터미널 실행하기</a>
<a href="http://blog.naver.com/PostView.nhn?blogId=dotnetulsan&logNo=221049552960" target="_blank">mysql 에러 설정하기</a>

mysql workbench로 가서 외부주소 포트번호 계정 비밀번호를 입력하면 접속이 된다.
이제 Data Import를 통해서 모든 데이터를 넣어준다.

이제 마지막으로 나의 스프링 프레임워크 서버와 나스의 mariaDB를 연동하는 것만 남았다.

8. pom.xml에서 mariaDB의 dependency를 추가해줍니다.
"'<!-- https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client -->
		<dependency>
		    <groupId>org.mariadb.jdbc</groupId>
		    <artifactId>mariadb-java-client</artifactId>
		    <version>1.5.8</version>
		</dependency>"'
jdbc 설정을 변경합니다.
mysql
"'
driver=com.mysql.jdbc.Driver
url=jdbc:mysql:lcalhost:3456/DB_NAME
"'
mariaDB
"'
driver=org.mariadb.jdbc.Driver
url=jdbc:mariadb://localhost:3456/DB_NAME
"'
보면 mariaDB는 ":"뒤에 "//"이 붙는다. 이제 외부주소로 들어가서 데이터베이스도
정상적으로 연결되었는지 확인하자.

추가.
우리가 전에 사용하던 aws ubuntu에서는 tomcat이 설치된 경로는 /var/lib/tomcat7/webapps/이었는데
nas의 경로는 /volume1/Tomcat/으로 이미지 업로드나 경로 변경에 관련된 작업을 해줘야 한다.
내일은 git을 블로그로 변경하는 작업을 진행해보도록 하겠습니다.
