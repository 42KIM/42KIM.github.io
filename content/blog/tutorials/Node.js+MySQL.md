---
title: "[Docs] Node.js + MySQL"
date: "2021-05-07"
tags: ["tutorial", "nodejs", "mysql"]
---
프로젝트에서 회원가입 기능을 구현하는 단계에 돌입했다. 신규 유저로부터 입력받은 회원정보를 보관하고 관리하기 위해 ```MySQL```을 ```Node.js```에 연동해야 했다. 이 과정을 간단하게 설명하고, 익숙치 않은 SQL 문을 기억해두기 위해 자주 사용하는 내용을 정리해둔다.



### Node.js에 MySQL 연동하기

먼저 ```npm```을 통해 [```mysql```](https://www.npmjs.com/package/mysql) 모듈을 설치한다.

```javascript
$ npm install mysql
```

MySQL 서버에 애플리케이션을 연결하는 방법은 다음과 같다.

``` javascript
const mysql = require('mysql');
const db = mysql.createConnection({
    // MySQL 서버의 위치
    // MySQL User ID
    // MySQL PASSWORD
    // 사용할 DATABASE 명
  	host     : 'localhost',
  	user     : 'me',
	password : 'secret',
  	database : 'my_db'
});

// MySQL 서버 접속
db.connect();
 
// DB에 Query 날리기
db.query('SELECT 1 + 1 AS solution', function (error, results, fields) {
  if (error) throw error;
  console.log('The solution is: ', results[0].solution);
});
 
// MySQL 서버와의 연결 종료
connection.end();
```

```db.query()```의 첫 번째 인자로는 실행할 SQL 명령어를, 두 번째 인자로는 콜백 함수가 위치한다. 콜백 함수의 첫 번째 인자로는 Query 처리 과정에서 발생하는 Error가, 두 번째 인자로는 Query의 결과가, 세 번째 인자로는 두 번째 인자의 자세한 필드 정보가 담긴다.

#### SQL Injection과 escape 방식

query() 메서드의 첫 번째 인자인 SQL문을 작성할 때, 주의할 점이 있다. SQL문에 데이터의 정보를 그대로 노출할 경우, ```SQL Injection```이라는 해킹 기법에 취약하다. SQL Injection은 우리가 데이터베이스 서버에 날리는 query를 탈취하여 데이터를 훔치거나, 데이터베이스 전체를 삭제하는 등의 큰 문제를 일으킬 수 있다. 이 같은 피해를 방지하기 위해 query를 보호하기 위한 ```escape```방식을 사용해야 한다. 

users 테이블에 회원의 id와 password를 저장하는 query를 날린다고 가정해보자.

```javascript
'INSERT INTO users (id, password) VALUES ('abc123', 'def456')'
```

위와 같은 SQL문이 query() 메서드의 인자로 전달된다. 만약 이 query가 SQL Injection에 의해 탈취 당한다면, 회원 ID와 PASSWORD는 고스란히 노출될 수밖에 없다.  이를 방지하기 위해 쉽게 사용할 수 있는 escape 방식은 다음과 같은 것들이 있다.

+ placeholder 사용하기

```javascript
db.query('INSERT INTO users (id, password) VALUES (?, ?)',
	     ['my-id', 'my-password'],
         function (error, results, fields) {
  			if (error) throw error;
  			console.log('The solution is: ', results[0].solution);
});
```

값 대신 ```?```를 위치시키고, query 메서드의 다음 인자로 값이 담긴 배열을 전달하는 방식이다. 이때, 물음표와 배열 의 요소가 순서대로 대입된다는 사실에 유의하여 값을 담아야 한다. 위의 코드에서 첫 번째 물음표에는 'my-id'가, 두 번째 물음표에는 'my-password'가 대입된다.

+ mysql 모듈이 제공하는 escape() 메서드 사용하기

```javascript
db.query('SELECT email FROM users WHERE id=' + db.escape('abc123')),
	function (error, results, fields) {
  		if (error) throw error;
  		console.log('The solution is: ', results[0].solution);
});
```



### SQL 문

**DATABASE**

```mysql
SHOW DATABASES; 			 // 목록 보기
CREATE DATABASE [DB명];		// 생성하기
USE [DB명];					// 사용하기
```

**TABLE**

```mysql
SHOW TABLES;									// 목록 보기
CREATE TABLE [테이블명] (
[컬럼명1] [데이터 타입](사이즈) [속성1] [속성2],
[컬럼명2] [데이터 타입](사이즈) [속성],
PRIMARY KEY(컬럼명)
);												// 생성하기
DESCRIBE [테이블명];							 // 정보 확인하기
```

**DATA**

```mysql
INSERT INTO [테이블명] ([컬럼명1], [컬럼명2]) VALUES ('값1', '값2');	// 데이터 추가하기

SELECT * FROM [테이블명];									 // 테이블의 모든 데이터 출력
SELECT [컬럼명] FROM [테이블명];							   // 테이블의 특정 컬럼 데이터 출력
SELECT [컬럼명] FROM [테이블명] WHERE [컬럼명]='값';		    // 특정 값을 포함하는 행 출력
SELECT [컬럼명] FROM [테이블명] LIMIT [숫자];				 // 출력 갯수 제한
SELECT * FROM [테이블명] ORDER BY [컬럼명] DESC;			  // 특정 컬럼을 기준으로 내림차순

UPDATE [테이블명] SET [컬럼명1]='수정값' WHERE [컬럼명2]='값'; // 컬럼의 값을 새로운 값으로 수정
DELETE FROM [테이블명] WHERE [칼럼명]='값'					 // 특정 값을 가진 행 삭제
```

**주의!**  UPDATE나 DELETE를 할 때는 **WHERE**를 절대 절대 빠트려서는 안된다. 전체 데이터가 수정될 위험이 있다.



#### 참고

[npm-mysql](https://www.npmjs.com/package/mysql)  
[MySQL DOCUMENTATION](https://dev.mysql.com/doc/)  
[Preventing SQL injection in Node.js (and other vulnerabilities)](https://blog.sqreen.com/preventing-sql-injection-in-node-js-and-other-vulnerabilities/)