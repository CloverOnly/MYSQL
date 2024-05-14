# MYSQL
 #DB만들기
 
 #데이터 정의어(Data Definition Language, DDL) : 데이터베이스 내에 테이블이나 인덱스, 뷰 등의 객체를 만들거나 수정, 삭제할 때 사용하는 언어를 의미함
#DDL에는 create, alter, drop이 있음
#char : 고정길이 문자형 데이터타입, 지정한 길이보다 데이터 길이가 작으면 빈칸만큼 공백(blank)이 들어감
#varchar : 가변길이 문자형 데이터타입, 데이터의 길이만큼의 메모리만 차지함
#text : varchar과 달리 길이를 지정하지 않음, 컬럼의 최대 길이를 모르는 경우에 사용하기 적합함

/*
숫자형 데이터 타입
정수형: 들어갈 데이터의 크기에 따라 tinyint부터 bigint 중에서 선택하여 지정할 수 있음
실수형: float형, double형, decimal형 중에서 선택하여 지정할 수 있음

날짜시간형 데이터 타입
date는 날짜, time은 시간을 저장하기 위한 데이터 타입
datetime과 timestamp는 날짜와 시간을 함께 저장할 수 있는 데이터타입


이진형 데이터타입
binary 또는 blob(binary Large Object) 데이터타입
Binary(n)와 BYTE(n)는 CHAR 형태의 이진형 데이터타입
VARBINARY(n)는 VARCHAR 형태의 이진형 데이터타입
BLOB도 저장 크기에 따라 TINYBLOB, BLOB, MEDIUMBLOB, LONGBLOB 등의 데이터타입을 사용할 수 있음

공간형 데이터타입 
공간 데이터를 저장하기 위한 데이터타입
GEOMETRY, POINT,LINESTRING, POLYGON, MULTIPONINT, MULTILINESTING, MULTIPOLYGON, GEOMETRYCOLLECTION 등이 있음
*/

#CREATE : 데이터베이스나 테이블, 뷰, 인덱스 등 객체를 만들 때 사용함

#한빛학사 데이터베이스를 생성하시오
CREATE DATABASE 한빛학사;		#한빛무역 같은 데이터 베이스 생성

use 한빛학사;		#한빛학사 안에 만들겠다

#테이블 생성 : 테이블을 생성할 때 모든 컬럼에는 데이터타입을 지정해주어야 함

#다음과 같이 한빛학사 데이터베이스 학과 테이블을 생성하고, 레코드 3건을 삽입하시오
#학과 테이블 생성
CREATE TABLE 학과 ( 학과번호 CHAR(2)
					,학과명 VARCHAR(20)
                    ,학과장명 VARCHAR(20));
#레코드 삽입
INSERT INTO 학과 VALUES('AA', '컴퓨터공학과', '배경민')
					,('BB', '소프트웨어학과', '김남준')
                    ,('CC', '디자인융합학과', '박선영');

#확인
SELECT * FROM 학과;

#다음과 같이 학생 테이블을 생성하고, 레코드 3건을 삽입하시오
#테이블 생성
CREATE TABLE 학생 ( 학번 CHAR(5)
				,이름 VARCHAR(20)
                ,생일 DATE		#DATE는 숫자지만 ''다음표는 특이성이다.
                ,연락처 VARCHAR(20)
                ,학과번호 CHAR(2));
#레코드 삽입
INSERT INTO 학생 VALUES('S0001', '이윤주', '2020-01-30', '01077778888', 'AA')
					,('S0001', '이승은', '2021-02-23', NULL, 'AA')
                    ,('S0003', '백재용', '2018-03-31', '01077778888', 'DD');
                    
#확인
SELECT * FROM 학생;

#학생 테이블을 사용하여 휴학생 테이블을 생성하시오. 이때 데이터는 복사하지 않고, 구조만 복사하시오
#WHERE을 사용하여 구조만 복사
CREATE TABLE 휴학생 AS SELECT *
					FROM 학생
                    WHERE 1 = 2;		#학생 테이블의 구조만 사용할때 쓴다 , 데이터 까지 가지고 오려면 WHERE안쓰면 된다.

#확인
SELECT * FROM 휴학생;

#GENERATED : 테이블에 있는 컬럼을 기반으로 하여 계산된 값을 저장할 수 있는 컬럼
#VIRTUAL : 기본값, 데이터는 저장하지 않고 정의만 데이터 사전에 추가하는 방식
#STORED : 레코드가 삽입되거나 수정될 때마다 값이 계산되어 저장되기 때문에 추가 저장 공간이 필요하지만, SELECT 검색 시에는 계산 없이 바로 값을 읽어올 수 있음

#휘트니스센터의 회원을 관리하는 테이블을 만드시오. 이때 체질량 지수가 자동 계산되어 저장하도록 GENERATED 컬럼으로 성정하시오
#회원 테이블 생성
CREATE TABLE 회원 ( 아이디 VARCHAR(20) PRIMARY KEY
					,회원명 VARCHAR(20)
                    ,키 INT
                    ,몸무게 INT
                    ,체질량지수 DECIMAL(4,1) AS (몸무게 / POWER(키 / 100, 2)) STORED);		#POWER 는 키 / 100하고 2승을 한다는 뜻 
#레코드 삽입
INSERT INTO 회원(아이디, 회원명, 키, 몸무게)		#체질량 지수는 계산되어 출력된다.
		VALUES('APPLE', '김사과', 178, 70); 	
#확인 
SELECT * FROM 회원;


#학생 테이블에 성별 컬럼을 추가하시오
ALTER TABLE 학생 ADD 성별 CHAR(1);		#ADD 열추가

ALTER TABLE 학생 MODIFY 성별 VARCHAR(2);		#MODIFY 테이터 타입 변경 CHAR(1) -> VARCHAR(2)

#학생 테이블에서 연락처 컬럼명을 휴대폰으로 변경하시오
ALTER TABLE 학생 CHANGE COLUMN 연락처 휴대폰번호 VARCHAR(20);		#CHANGE 컬럼명 변경

#학생 테이블에서 성별 컬럼을 삭제하시오
ALTER TABLE 학생 DROP column 성별;		#DROP 삭제 /항상 주의해서 써야함

#휴학생 테이블명을 졸업생 테이블명으로 변경하시오
ALTER TABLE 휴학생 RENAME 졸업생;		#RENAME 테이블명 병경

#확인
SELECT * FROM 학생;

#DROP : 데이터베이스, 테이블 및 기타 여러 객체를 삭제할 때 사영
#학과 테이블과 학생 테이블을 삭제하시오
DROP TABLE 학과;
DROP TABLE 학생;

/*
제약조건의 개념
제약조건
데이터베이스에는 무결한 데이터가 들어가야 함
테이블에 아무런 제약 사항을 두지 않으면 적합하지 않은 데이터가 저장되어 묵ㄹ성 위배가 발생할 수 있음
이를 위해 테이블에 제약조건을 설정하여 데이터 부결성을 유지할 수 있음

제약조건의 특징
제약조건은 데이터 사전에 저장됨
제약 조건은 CREATE문으로 테이블을 생성할 때 지정할 수 있고, ALTER문으로 테이블의 구조를 변경햘 때 지정할 수도 있음
제약조건은 고유한 이름을 붙여서 식별할 수 있음
한 컬럼에 여러 개의 제약조건을 설정할 수 있음

제약조건의 종류
PRIMARY KEY  제약
기본키를 설정하는 제약조건
기본키는 레코드를 대표하는 키로 한 개 이상의 컬럼으로 구성됨
기본키는 테이블당 한 개여야 함
기본키를 생성하면 자동으로 인덱스가 생성됨
기본키는 NOT NULL이어야 하고, 유일한 값을 가져야 함 (NOT NULL 제약조건 + UNIQUE 제약조건)

NOT NULL 제약
NOT NULL 제약조건이 설정된 컬럼에는 반드시 값을 넣어야 함
NOT NULL 제약조건은 반드시 컬럼 레벨로 설정해야 함

UNIQUE 제약
UNIQUE 제약조건이 설정된 컬럼에는 반드시 유일한 값을 넣여야 함
자동으로 인덱스가 생성됨

CHECK 제약
CHECK 제약조건이 설정된 컬럼에는 설정된 조건에 맞는 값만 넣어야 함
조건으로는 특정 값이나 범위, 특정 패턴의 숫자나 문자 값 등을 설정할 수 있음

DEFAULT 제약
값을 넣지 않을 경우 지정한 값이 자동으로 들어감

FOREIGN KEY 제약
외래키를 설정하는 제약조건
한 테이블의 외래키는 참조하는 테이블의 기본키이거나 NULL이어야 함
외래키의 컬럼과 참조하는 테이블의 기본키 컬럼의 데이터타입과 크기는 서로 동일해야 함
*/

#제약조건을 추가하여 학과 테이블을 다시 생성하시오
#방법 1 : 컬럼 레벨의 제약조건 설정 => 시키면서 제약
CREATE TABLE 학과 (학과번호 CHAR(2) PRIMARY KEY
					,학과명 VARCHAR(20) NOT NULL
                    ,학과장명 VARCHAR(20));
                    
#방법2 : 테이블 레벨로 제약조건 설정 => 생성후 제약
CREATE TABLE 학과 (학과번호 CHAR(2)
					,학과명 VARCHAR(20) NOT NULL
                    ,학과장명 VARCHAR(20)
                    ,PRIMARY KEY(학과번호));
                    
#제약조건을 추가하여 학생 테이블을 다시 생성하시오
#방법 1 : 컬럼 레벨의 제약조건 설정
CREATE TABLE 학생 (학번 CHAR(5) PRIMARY KEY  
					,이름 VARCHAR(20) NOT NULL		#NOT NULL 입력 안하면 오류
                    ,생일 DATE NOT NULL
                    ,연락처 VARCHAR(20) UNIQUE		#UNIQUE 중복 안됨 
                    ,학과번호 CHAR(2) REFERENCES 학과(학과번호)		#PEFERENCES 참고
                    #학과가 부모인데 프라이머리키여야만 가능하고, 먼저 있어야 PEFERENCES를 쓸수잇다. 학과번호는 자식
                    ,성별 CHAR(1) CHECK(성별 IN ('남','여'))
                    ,등록일 DATE DEFAULT(CURDATE())
                    ,FOREIGN KEY(학과번호) REFERENCES 학과(학과번호));		#외래키작업
                    
#방법2 : 테이블 레벨로 제약조건 설정
CREATE TABLE 학생 (학번 CHAR(5) 
					,이름 VARCHAR(20) NOT NULL
                    ,생일 DATE NOT NULL 
                    ,연락처 VARCHAR(20)
                    ,학과번호 CHAR(20)
                    ,성별 CHAR(1)
                    ,등록일 DATE DEFAULT(CURDATE())
                    ,PRIMARY KEY(학번)
                    ,UNIQUE(연락처)
                    ,CHECK(성별 IN ('남', '여'))
                    ,FOREIGN KEY(학과번호) REFERENCES 학과(학과번호));
                    
#제약조건을 추가하여 과목 테이블을 생성하시오
CREATE TABLE 과목 (과목번호 CHAR(5) PRIMARY KEY
					,과목명 VARCHAR(20) NOT NULL		
                    ,학점 INT NOT NULL CHECK(학점 BETWEEN 2 AND 4)
                    ,구분 VARCHAR(20) CHECK(구분 IN ('전공','교양','일반')));
                    #CHECK (구분)만 필수가 아니라 다른건 값이 필수로 다 들어가야한다.
                    
#제약조건을 추가하여 수강_1 테이블을 생성하시오 기본키는 수강년도, 수강학기, 학번, 과목번호를 모두 포함하시오
CREATE TABLE 수강_1 (수강년도 CHAR(4) NOT NULL
					,수강학기 VARCHAR(20) NOT NULL CHECK(수강학기 IN ('1학기', '2학기', '여름학기', '겨울학기'))
                    ,학번 CHAR(5) NOT NULL
                    ,과목번호 CHAR(5) NOT NULL
                    ,성적 DECIMAL(3,1) CHECK(성적 BETWEEN 0 AND 4.5)		#NUMERIC -> DECIMAL로 변경 
                    ,PRIMARY KEY(수강년도, 수강학기, 학번, 과목번호)
                    ,FOREIGN KEY(학번) REFERENCES 학생(학번)
                    ,FOREIGN KEY(과목번호) REFERENCES 과목(과목번호));
                    
#수강번호라는 대리키를 기본키로 하는 제약조건을 추가하여 수강_2 테이블을 생성하시오. 수강번호는 일련번호로 생성하시오.
CREATE TABLE 수강_2(수강번호 INT PRIMARY KEY AUTO_INCREMENT		#AUTO_INCREMENT 자동으로 일렬번호 생성
					,수강년도 CHAR(4) NOT NULL
                    ,수강학기 VARCHAR(20) NOT NULL CHECK(수강학기 IN ('1학기','2학기','여름학기','겨울학기'))
					,학번 CHAR(5) NOT NULL
                    ,과목번호 CHAR(5) NOT NULL
                    ,성적 NUMERIC(3,1) CHECK(성적 BETWEEN 0 AND 4.5)
                    ,FOREIGN KEY (학번) REFERENCES 학생(학번)
                    ,FOREIGN KEY (과목번호) REFERENCES 과목(과목번호));

#학과 테이블에 다음과 같이 레코드 3건을 추가하시오. 오류가 발생한다면 이유가 무엇인지 생각해봅니다.
INSERT INTO 학과 VALUES ('AA', '컴퓨터공학과', '배경민');
/*INSERT INTO 학과 VALUES ('AA', '소프트웨어학과', '김남준') -> 오류발생 프라이머리키의 중복됨.*/ 
INSERT INTO 학과 VALUES ('BB', '소프트웨어학과', '김남준');
INSERT INTO 학과 VALUES ('CC', '디자인융합학과', '박선영');
#확인
SELECT * FROM 학과;

#학생 테이블에 다음과 같이 레코드 3건을 추가하시오. 오류가 발생한다면 이유가 무엇인지 생각해봅니다.
INSERT INTO 학생(학번, 이름, 생일, 학과번호) VALUES ('S0001','이윤주','2020-01-03','AA');
/*INSERT INTO 학생(이름, 생일, 학과번호) VALUES('이승은', '2021-02-23', 'AA'); -> 오류: 프라이버리 키값이 빠짐*/
INSERT INTO 학생(학번, 이름, 생일, 학과번호) VALUES('S0002', '이승은', '2021-02-23', 'AA');
/*INSERT INTO 학생(학번, 이름, 생일, 학과번호) VALUES('S0003','백재용', '2018-03-31','DD'); -> 오류: 학과번호에서 DD는 존재하지 않음*/
INSERT INTO 학생(학번, 이름, 생일, 학과번호) VALUES('S0003','백재용', '2018-03-31','CC');   
#확인
SELECT * FROM 학생;

/* 참고
CREATE TABLE 과목 (과목번호 CHAR(5) PRIMARY KEY
					,과목명 VARCHAR(20) NOT NULL		
                    ,학점 INT NOT NULL CHECK(학점 BETWEEN 2 AND 4)
                    ,구분 VARCHAR(20) CHECK(구분 IN ('전공','교양','일반')));
*/
#과목 테이블에 다음과 같이 레코드 3건을 추가하시오. 오류가 발생한다면 이유가 무엇인지 생각해봅니다.
/*INSERT INTO 과목(과목번호, 과목명, 구분) VALUES ('C0001', '데이터베이스실습', '전공');-> 오류: 학점 빠짐*/
INSERT INTO 과목(과목번호, 과목명, 구분, 학점) VALUES ('C0001', '데이터베이스실습', '전공', 3);
/*INSERT INTO 과목(과목번호, 과목명, 구분, 학점) VALUES('C0002', '데이터베이스 설계와 구축', '전공', 5);-> 오류: 학점이 범위를 벗어남*/
INSERT INTO 과목(과목번호, 과목명, 구분, 학점) VALUES('C0002', '데이터베이스 설계와 구축', '전공', 3);
INSERT INTO 과목(과목번호, 과목명, 구분, 학점) VALUES('C0003', '데이터 분석', '전공', 3);

/* 참고
CREATE TABLE 수강_1 (수강년도 CHAR(4) NOT NULL
					,수강학기 VARCHAR(20) NOT NULL CHECK(수강학기 IN ('1학기', '2학기', '여름학기', '겨울학기'))
                    ,학번 CHAR(5) NOT NULL
                    ,과목번호 CHAR(5) NOT NULL
                    ,성적 DECIMAL(3,1) CHECK(성적 BETWEEN 0 AND 4.5)		#NUMERIC -> DECIMAL로 변경 
                    ,PRIMARY KEY(수강년도, 수강학기, 학번, 과목번호)
                    ,FOREIGN KEY(학번) REFERENCES 학생(학번)
                    ,FOREIGN KEY(과목번호) REFERENCES 과목(과목번호));
*/
#수강_1 테이블에 다음과 같이 레코드 4건을  추가하시오. 오류가 발생한다면 이유가 무엇인지 생각해봅니다.
INSERT INTO 수강_1(수강년도, 수강학기, 학번, 과목번호, 성적) VALUES('2023', '1학기', 'S0001', 'C0001', 4.3);
/*INSERT INTO 수강_1(수강년도, 수강학기, 학번, 과목번호, 성적) VALUES('2023', '1학기', 'S0001', 'C0001', 4.4); -> 오류:PRIMARY KEY(수강년도, 수강학기, 학번, 과목번호)가 위에꺼랑 전부 일치함 중복오류 */
/*INSERT INTO 수강_1(수강년도, 수강학기, 학번, 과목번호, 성적) VALUES('2023', '1학기', 'S0001', 'C0002', 4.6); -> 오류: 성적이 CHECK범위를 벗어남*/
INSERT INTO 수강_1(수강년도, 수강학기, 학번, 과목번호, 성적) VALUES('2023', '1학기', 'S0001', 'C0002', 4.4);
/*INSERT INTO 수강_1(수강년도, 수강학기, 학번, 과목번호, 성적) VALUES('2023', '1학기', 'S0002', 'C0009', 4.3); -> 오류: 과목번호가 과목테이블에 존재하지 않음, REFERENCES 과목(과목번호)썼기때문에 주의해서 봐야함*/
INSERT INTO 수강_1(수강년도, 수강학기, 학번, 과목번호, 성적) VALUES('2023', '1학기', 'S0002', 'C0003', 4.3);

#수강_1에서 넣은 것과 동일하게 수강_2 테이블에 2개의 레코드를 추가해보시오.
INSERT INTO 수강_2(수강년도, 수강학기, 학번, 과목번호, 성적) VALUES('2023', '1학기', 'S0001', 'C0001', 4.3);
INSERT INTO 수강_2(수강년도, 수강학기, 학번, 과목번호, 성적) VALUES('2023', '1학기', 'S0001', 'C0001', 4.5);

#제약조건의 추가
#학생 테이블의 학번 컬러머에 CHECK 제약조건을 추가하시오
#제약조건: 모든 학번은 'S'로 시작한다
ALTER TABLE 학생 ADD CONSTRAINT CHECK(학번 LIKE 'S%');

#학생 테이블에 설정되어 있는 제약조건 명세를 확인하시오.
SELECT * FROM INFORMATION_SCHEMA.TABLE_CONSTRAINTS 
		 WHERE CONSTRAINT_SCHEMA = '한빛학사' AND TABLE_NAME = '학생';
#INFORMATION_SCHEMA.TABLE_CONSTRAINTS: 제약조건 명세

#제약조건의 삭제
#학생 테이블에 설정되어 있는 제약조건 중 연락처에 설정되어 있는 제약조건을 삭제하시오
ALTER TABLE 학생 DROP CONSTRAINT 연락처;
#CONSTRAINT: 제약 EX) CHECK, UNIQUE

#성별 컬럼에 설정되어 있는 CHECK 제약조건을 삭제하시오
#학생 테이블에 걸려있는 CHECK 제약조건 2개를 삭제하고 학번에 설정했던 CHECK제약조건은 다시 설정
#ALTER TABLE 학생 DROP CONSTRAINT 학생_chk_1;
#ALTER TABLE 학생 DROP CONSTRAINT 학생_chk_2;
ALTER TABLE 학생 ADD CHECK (학번 LIKE 'S%');

#제약조건명의 지정: 제약조건에는 고유한 이름을 지정하여 관리할 수 있음
#학생 테이블의 구조를 사용하여 학생_2 테이블을 생성하시오. 이때 제약조건명을 지정하여 생성하시오
#학생_2 테이블 생성
CREATE TABLE 학생_2(학번 CHAR(5)
				   ,이름 VARCHAR(20) NOT NULL
                   ,생일 DATE NOT NULL
                   ,연락처 VARCHAR(20)
                   ,학과번호 CHAR(2)
                   ,성별 CHAR(1)
                   ,등록일 DATE DEFAULT(CURDATE())
                   ,PRIMARY KEY(학번)
                   ,CONSTRAINT UK_학생2_연락처 UNIQUE(연락처)	#제약조건에 명칭 부여 제약조건 명칭은 제약조건명약자_테이블명_필드명으로 만드는게 좋다
                   ,CONSTRAINT CK_학생2_성별 CHECK(성별 IN ('남','여'))
                   ,CONSTRAINT FK_학생2_학과번호 FOREIGN KEY (학과번호) REFERENCES 학과(학과번호));

#학생 테이블의 구조를 사용하여 학생_2 테이블을 생성하시오. 이때 제약조건명을 지정하여 생성하시오.
#제약조건 명세 확인
SELECT *
	  FROM INFORMATION_SCHEMA.TABLE_CONSTRAINTS
      WHERE CONSTRAINT_SCHEMA = '한빛학사'
      AND TABLE_NAME = '학생_2';
      
#수강평가 테이블을 생성하시오. 이때 평가순번은 자동번호로 생성합니다. 또한 과목 테이블에서 레코드를 삭제하면 수강평가 테이블에서도 해당 과목에 대한 평가 레코드가 삭제되도록 옵션을 설정하시오.
#수강평가 테이블 생성
CREATE TABLE 수강평가(평가순번 INT PRIMARY KEY AUTO_INCREMENT	#PRIMARY KEY이 AUTO_INCREMENT로 인해 자동생성
					,학번 CHAR(5) NOT NULL
                    ,과목번호 CHAR(5) NOT NULL
                    ,평점 INT CHECK(평점 BETWEEN 0 AND 5)
                    ,과목평가 VARCHAR(500)
                    ,평가일시 DATETIME DEFAULT CURRENT_TIMESTAMP	#DEFAULT: 값을 넣지 않을때 TIMESTAMP: 날짜값 ->값을 넣지 않아도 날짜값이 자동으로 들어감
                    ,FOREIGN KEY (학번) REFERENCES 학생(학번)
                    ,FOREIGN KEY (과목번호)  REFERENCES 과목(과목번호) ON DELETE CASCADE);	#ON DELETE CASCADE: 삭제된다면 연속적으로

#수강평가 테이블에 4건의 레코드를 추가하시오
INSERT INTO 수강평가(학번, 과목번호, 평점, 과목평가)
		VALUES('S0001', 'C0001', 5, 'SQL학습에 도움이 되었습니다.')
			  ,('S0001', 'C0003', 5, 'SQL활용을 배워서 좋았습니다.')
              ,('S0002', 'C0003', 5, '데이터 분석에 관심이 생겼습니다.')
              ,('S0003', 'C0003', 5, '머신러닝과 시각화 부분이 유용했습니다.');
              #평가순번과 평가일시는 자동으로 들어간다.
#확인
SELECT * FROM 수강평가;

#과목 테이블에서 'C0003'과목을 삭제하시오. 그리고 과목 테이블과 수강평가 테이블에서 결과를 확인해봅니다.
DELETE FROM 과목 WHERE 과목번호 = 'C0003';
SELECT * FROM 과목;
SELECT * FROM 수강평가;

#과목 테이블에서 'C0001'과목을 삭제하시오. 오류가 난다면 이유을 생각해봅니다.
DELETE FROM 과목 WHERE 과목번호 = 'C0001';

#문제
#DB생성
CREATE DATABASE tableDB;

use tableDB;

CREATE TABLE 회원테이블(아이디 VARCHAR(20) PRIMARY KEY
						,이름 CHAR(5) NOT NULL
                        ,생년 DATE NOT NULL
                        ,지역 VARCHAR(20)
                        ,국번 VARCHAR(20)
                        ,전화번호 VARCHAR(20) UNIQUE
                        ,키 INT
                        ,가입일 DATE DEFAULT(CURDATE()));

CREATE TABLE 구매테이블(순번 INT PRIMARY KEY AUTO_INCREMENT
						,아이디 VARCHAR(20) NOT NULL
                        ,물품  VARCHAR(20) NOT NULL
                        ,분류 VARCHAR(20) 
                        ,단가 INT NOT NULL
                        ,수량 INT 
                        ,FOREIGN KEY (아이디) REFERENCES 회원테이블(아이디) ON DELETE CASCADE);
                        
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 국번, 전화번호, 키) 
		VALUES('YSJ', '유재석', '1972-01-01', '서울', '010', '1111111', 178);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 국번, 전화번호, 키) 
		VALUES('KHD', '강호동', '1970-01-01', '경북', '019', '22222', 182);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 국번, 전화번호, 키) 
		VALUES('KKJ', '김국진', '1966-01-01', '', '019', '333333', 171);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 국번, 전화번호, 키) 
		VALUES('KYM', '김용만', '1967-01-01', '서울', '010', '444444', 177);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 키) 
		VALUES('KJD', '김제동', '1974-01-01', '경남', 173);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 국번, 전화번호, 키) 
		VALUES('NHS', '남희석', '1971-01-01', '충남', '016', '666666', 180);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 키) 
		VALUES('SDY', '신동엽', '1971-01-01', '경기', 176);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 국번, 전화번호, 키) 
		VALUES('LHK', '이휘재', '1972-01-01', '경기', '011', '8888888', 180);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 국번, 전화번호, 키) 
		VALUES('LKK', '이경규', '1960-01-01', '경남', '018', '999999', 170);
INSERT INTO 회원테이블(아이디, 이름, 생년, 지역, 국번, 전화번호, 키) 
		VALUES('PSH', '박수홍', '1970-01-01', '서울', '010', '000000', 183);
SELECT * FROM 회원테이블;

INSERT INTO 구매테이블(아이디, 물품, 단가, 수량) 
		VALUES('YSJ', '운동화', 30, 2);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('KHD', '노트북','전자', 1000, 1);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('KYM', '모니터','전자', 200, 1);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('PSH', '모니터','전자', 200, 5);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('KHD', '청바지','의류', 50, 3);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('PSH', '메모리','전자', 80, 10);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('KJD', '책','서적', 15, 5);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('LHK', '책','서적', 15, 2);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('LHK', '청바지','의류', 50, 1);
INSERT INTO 구매테이블(아이디, 물품, 단가, 수량) 
		VALUES('PSH', '운동화', 30, 2);
INSERT INTO 구매테이블(아이디, 물품, 분류, 단가, 수량) 
		VALUES('LHK', '책','서적', 15, 1);
INSERT INTO 구매테이블(아이디, 물품, 단가, 수량) 
		VALUES('PSH', '운동화',30, 2);

SELECT * FROM 구매테이블;

SELECT * FROM 회원테이블, 구매테이블;

        
        
        
