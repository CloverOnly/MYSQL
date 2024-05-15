SQL 5.16 배울 내용 예습 

#뷰(VIEW) : 한 개 이상의 테이블을 기반으로 생성된 가상의 테이블, 실제 데이터를 저장하지 않으며, 쿼리 실행 시점에 생성된 쿼리 결과를 가상의 테이블로 만들어서 제공함
#다시 말해 뷰는 물리적으로 데이터를 저장하는 것이 아니라 SELECT문을 저장한 형태로서 스토어드 쿼리(STORED QUERIES)라고도 함, 뷰실행 시에는 테이블을 조회한 것과 유사한 형태로 데이터를 확인할 수 있음
/*
뷰 장점
데이터 중목성 최소화
데이터 보안성
간편한 데이터 접근
데이터 가상화

뷰 생성
형식1 : OR REPLACE
형식2 : ALTER
*/

#예제 9-1
#한빛무역 데이터베이스에서 사원 테이블을 사용하여 사원의 이름, 집전화, 입사일, 주소를 보이는 뷰를 작성하시오
#뷰를 생성할 때 컬럼명에 별명을 붙일 수 있으며, 뷰를 사요야할 때에는 컬럼명에 붙인 별명을 사용해야 함
CREATE OR REPLACE VIEW view_사원 AS SELECT 이름, 집전화 AS 전화번호, 입사일, 주소 FROM 사원;
/*
컬럼 또는 컬럼의 별명을 뷰 이름 옆에 나열할 수도 있음
CREATE OR REPLACE VIEW view_사원(이름, 전화번호, 입사일, 주소) 
						AS SELECT 이름, 집전화, 입사일, 주소 FROM 사원;
*/
#뷰명 view_사원 / 뷰를 생성한 이후에는 뷰를 테이블인 것처럼 조회할 수 있음
SELECT * FROM view_사원;

#예제 9-2
#제품 테이블, 주문세부 테이블을 조인하여 제품명과 주문수량합을 보이는 뷰를 작성하시오
#조인이나 서브쿼리 등 여러 테이블을 사용한 쿼리문으로 뷰를 생성 가능
CREATE OR REPLACE VIEW view_제품별주문수량합 AS SELECT 제품명, SUM(주문수량) AS 주문수량합 FROM 제품
											INNER JOIN 주문세부 ON 제품.제품번호 = 주문세부.제품번호 GROUP BY 제품명;
#확인
SELECT * FROM view_제품별주문수량합;

#예제 9-3
#'여' 사원에 대하여 사원의 이름, 집전화, 입사일, 주소, 성별을 보이는 뷰를 작성하시오
CREATE OR REPLACE VIEW view_사원_여 AS SELECT 이름, 집전화 AS 전화번호, 입사일, 주소, 성별 FROM 사원 WHERE 성별 = '여';
#확인
SELECT * FROM view_사원_여;	

#예제 9-4
#예제 9-3에서 생성한 'view_사원_여' 뷰로 전화번호에 '88'이 들어간 사원의 정보를 검색하시오.
SELECT * FROM view_사원_여 WHERE 전화번호 LIKE '%88%';

#예제 9-5
#예제 9-2에서 생성한 'view_제품별주문수량합' 뷰로 주문수량합이 1,200개 이상인 레코드를 검색하시오
SELECT * FROM view_제품별주문수량합 WHERE 주문수량합 >= 1200;

/*
뷰 메타 정보 확인하기
형식1: INFORMATION_SCHEMA.VIEWS
형식2: SHOW CRATE VIEW
*/	

#예제 9-6
#'view_사원'뷰의 메타 정보를 확인하시오
SELECT * FROM INFORMATION_SCHEMA.VIEWS WHERE TABLE_NAME = 'view_사원';
SHOW CREATE VIEW view_사원;	


/*
뷰 삭제하기
형식: DROP VIEW 뷰명;
*/

#예제 9-7
#'view_사원'뷰를 삭제하시오
DROP VIEW view_사원;

/*
데이터 삽입 조건
삽입할 대상의 컬럼 명시
NOT NULL 제약조건
PRIMARY KEY 제약조건
CHECK 제약조건
WITH CHECK OPTION 조건
함수, 수식, JOIN, GROUP BY, UNION 등 사용 여부
*/					

#얘제 9-8
#예제 9-3에서 작성한 'view_사원_여' 뷰를 사용하여 다음 레코드를 삽입하시오. 오류가 발생한다면 이유를 생각해보시오
#이름: '황여름', 전화번호:'(02)587-4989',2023-02-10', '서울시 감남구 청담동 23-5', '여'
INSERT INTO view_사원_여(이름, 전화번호, 입사일, 성별) VALUES('황여름', '(02)587-4989', '2023-02-10','서울시 강남구 청담동 23-5', '여'); #오류남
SELECT * FROM view_사원_여;
#오류 해결
CREATE OR REPLACE VIEW view_사원_여 AS SELECT 사원번호, 이름, 집전화 AS 전화번호, 입사일, 주소, 성별 FROM 사원 WHERE 성별 = '여';		#사원번호 추가
ALTER VIEW view_사원_여 AS SELECT 사원번호, 이름, 집전화 AS 전화번호, 입사일, 주소, 성별 FROM 사원 WHERE 성별 = '여';
#사원번호: E12, 이름: 황여름, 전화번호: (02)587-4989, 입사일: 2023-02-10, 주소: 서울시 강남구 청담동 23-5, 성별: 여
INSERT INTO view_사원_여(사원번호, 이름, 전화번호, 입사일, 주소, 성별)
			VALUES('E12', '황여름', '(02)587-4989', '2023-02-10', '서울시 강남구 청담동 23-5', '여');
#확인
SELECT * FROM view_사원_여 WHERE 사원번호 = 'E12';

#뷰를 사용하여 레코드를 추가하였지만 실제로는 사원 테이블에 레코드가 삽입됨
SELECT * FROM 사원 WHERE 사원번호 = 'E12';

#예제 9-9
#예제 9-2에서 작성한 'view_제품별주문수량합'뷰를 사용하여 레코드를 삽입하시오. 오류가 난다면 이유를 생각해보시오
#제품명: 단짠 새우깡, 주문수량합: 250
INSERT INTO view_제품별주문수량합 VALUES('단짠 새우깡', 250);	#에러

/*
WITH CHECK OPTION
뷰에서 사용하는 옵션 중 하나
특정 뷰에 대해 INSERT 또는 UPDATE 작업을 수행할 때 해당 작업으로 인해 뷰에서 정의한 조건을 만족하지 않는 행이 생성되거나 수정되는 것을 방지함
WITH CHECK OPTION을 사용하면 뷰를 통해 데이터를 수정하거나 삽입할 때 뷰의 조건을 만족하는 데이터만 작업이 가능함
즉, 뷰에서 사용된 조건식을 기반으로 데이터의 일관성을 보장하기 위한 제약조건
*/

#예제 9-10
#'view_사원_여' 뷰를 사용하여 '남' 사원 정보를 추가하고, 결과를 확인하시오
#사원번호: E13, 이름: 강겨울, 입사일: 2023-02-10, 주소: 서울시 성북구 장위동 123-7, 성별, 남
INSERT INTO view_사원_여(사원번호, 이름, 입사일, 주소, 성별) 
			VALUES('E13', '강겨울', '2023-02-10', '서울시 성북구 장위동 123-7', '남');
#삽입된 레코드를 뷰로 확인
SELECT * FROM view_사원_여 WHERE 사원번호 =  'E13';	#삽입은 정상적으로 이루어졌으나 검색되지 않음
#테이블에서 레코드를 확인
SELECT * FROM 사원 WHERE 사원번호 = 'E13';	#테이블에는 존재

#해결방안
CREATE OR REPLACE VIEW view_사원_여 AS SELECT 사원번호, 이름, 집전화 AS 전화번호, 입사일, 주소, 성별 FROM 사원 WHERE 성별 = '여' WITH CHECK OPTION;

#새로운 '남' 사원을 추가해보기 (사원번호: E14, 이름: 유봄, 성별: 남)
INSERT INTO view_사원_여(사원번호, 이름, 성별) VALUES('E14', '유봄', '남');		#에러
#이름: 황여름, 성별: 남
UPDATE view_사원_여 SET 성별 = '남' WHERE 이름 = '황여름';		#에러

/*
인덱스(index)
데이터베이스 테이블에서 특정 컬림이나 컬럼의 집합에 대한 검색 성능을 향상시키기 위해 사용되는  자료구조
데이터베이스의 성능 향상을 위해 중요한 역할을 수행함

인덱스의 장점
검색속도 향상
정렬 및 순서 유지 
중복 제거
조인 성능 향상
쿼리 성능 향상

기본 인덱스(primary index)
클러스터형 인덱스(CLUSTERED INDEX)라고도 함
테이블의 기본키 컬럼에 대해 행 데이터가 오름차순으로 정렬되는 인덱스
기본 인덱스는 테이블당 한 개만 생성할  수 있음
기본 인덱스는 사전과 유사한 방식으로 저장됨

보조 인덱스(SECONDARY INDEX)
기본 인덱스 이외 인덱스로, 비클러스터형 인덱스(NON-CLUSTERED INDEX)라고도 함
보조 인덱스는 책의 찾아보기 페이지와 유사함

인덱스 생성 시 주의사항
쿼리 패턴 분석
테이블 크기와 메모리 제한
업데이트 비용 주의
복합 인덱스 
중복 및 NULL 값

B-트리 인덱스
데이터는 루트 노드부터탐색이 시작되고, 정렬된 인덱스키를 따라서 리프 노드까지 도달함
기본 인덱스는 리프 노드에 키가 포함된 행의 모든 데이터를 포함하므로 추가적인 데이터 조회가 필요하지 않음
반면 보조 인덱스는 먼저 인덱스키와 쌍이 되는 기본키 값을 찾은 후 기본 인덱스에서 검색함

인덱스 생성하기 
형식: CREATE [UNIQUE] INDEX 인덱스명 ON 테이블명(컬럼명);
테이블에 인덱스를 추가하는 형식
ALTER TABLE 테이블명 ADD INDEX 인덱스명 (컬럼명);
테이블에 인덱스를 삭제하는 형식
ALTER TABLE 테이블명 DROP INDEX 인덱스명 (컬럼명);
테이블에 걸려있는 인덱스를 확인하는 형식
SHOW INDEX FROM 테이블명;

인덱스 사용 시 고려 사항
인덱스가 다중 컬럼으로 구성되어 있다면 옵티마이저는 인덱스의 가장 왼쪽 접두사(LEFTMOST PREFIX)를 사용해서 컬럼을 찾게 됨
LIKE 비교에서 인수가 상수 문자열인 경우에는 인덱스를 사용할 수 있으나, '%'와 같은 와일드카드 문자로 시작하는 경우에는 인덱스를 사용할 수 없음
인덱스의 가장 왼쪽 접두사가 반드시 모든 AND 그룹에서 사용되어야 인덱스를 사용할 수 있음
*/

#예제 9-11
#날씨 테이블과 인텍스를 생성하시오.
/* 컬럼: 년도 INT, 월 INT, 일 INT, 도시 VARCHAR(20), 기운 NUMERIC(3,1), 습도 INT
기본키: 년도 + 월 + 일 + 도시
보조 인덱스: 기온, 도시 */
CREATE TABLE 날씨 (년도 INT
					,월 INT
                    ,일 INT
                    ,도시 VARCHAR(20)
                    ,기온 NUMERIC(3,1)
                    ,습도 INT
                    ,PRIMARY KEY(년도, 월, 일, 도시)
                    ,INDEX 기온인덱스(기온)
                    ,INDEX 도시인덱스(도시));
 
 #확인
SELECT * FROM 날씨;

#문제  수업듣고 풀어볼것
SELECT * FROM 사원;    
SELECT * FROM 부서;
CREATE OR REPLACE VIEW view_사원부서 AS SELECT 도시
											,사원  LIKE = '%대표이사%' AS 대표 이사
                                            ,부서명 = '영업부' AS 영업
                                            ,부서명 = '마케팅부' AS 마케팅
                                            ,회계
                                            FROM 사원
                                            INNER JOIN 부서
                                            ON 사원.부서번호 = 부서.부서번호
                                            GROUP BY 도시;

/*
SQL 스토어드 프로그램(STROED PROGRAM)
데이터베이스에서 실행되는 프로그램으로 일련의 DQL문을 포함하고 있는 데이터 베이스 객체
주로 프로시저나 함수, 트리거 형태로 사용됨

스토어드 프로그램의 특징
프로그래밍 언어적 트것ㅇ
코드 캡슐화와 유지 보수
재사용성
성능 향상
보안
트랙잭션 관리
비즈니스 로직 분리

변수 : 값을 저장하고 참조하는 데 사용되는 식별자
조건문
IF문 : 조건에 따라 다른 명령을 처리하고자 할 떄 사용, 조건이 여러 ㅓ개일 때에는 ELSE IF로 조건을 추가할 수 있음
		ELSEIF절은 여러 개 작성이 가능하지만, ELSE는 문장 내에서 한 번만 작성 가능
*/

#예제 10-1
#IF문을 사용하여 두 개의 숫자 10과 5의 크기를 비교하는 프로시저를 작성하시오
DELIMITER $$		#??
CREATE PROCEDURE proc_if()
	BEGIN
		DECLARE x INT;
		DECLARE Y INT DEFAULT 5;
		SET x = 10;
    
		IF x > y THEN 
			SELECT 'x는 y보다 큽니다.' AS 결과;
		ELSE
			SELECT 'x는 y보다 작거나 같습니다.' AS 결과;
		END IF;
	END $$
DELIMITER;		#ERROR CODE: 1304

#스토어드 프로시저를 실행할 때는 CALL을 사용함
CALL proc_if();		#실행 안됨

#CASE문 : 주로 복잡한 다중 조건을 처리하기 위해 사용	

#예제 10-2
#CASE문을 사용하여 숫자 10이 짝수인지 홀수인지를 판별하는  프로시저를 작성하시오
DELIMITER$$
CREATE PROCEDURE proc_case()
	BEGIN
		DECLARE x INT DEFAULT 10;
        DECLARE y INT;
        SET y = 10 mod  2;
        
        CASE
			WHEN  y = 0 THEN
				SELECT 'x는 짝수입니다.' AS '결과';
			ELSE
				SELECT 'x는 홀수입니다.' AS '결과';
		END CASE;
	END $$
DELIMITER;		#ERROR CODE: 1304

#확인
CALL proc_case()$$

#WHILE : 주어진 주건이 참이 ㄴ동안 반복적으로 코드를 실행하다가, 조건이 거짓이 되면 반복문을  종료함
#		WHILE문은 반복 시작하기 전에 조건을 평가하기 때문에 조건이 처음부터 거짓이면 코드가 실행되지 않음

#예제 10-3
#1부터 10까지의 합을 출력하는 프로시저를 WHILE문으로 작성하시오
DELIMITER $$
CREATE PROCEDURE proc_while()
	BEGIN
		DECLARE x INT DEFAULT 0;
        DECLARE y INT DEFAULT 0;
        
        WHILE x < 10 DO
			SET x =  x  + 1;
            SET y = y + x;
		END WHILE;
        SELECT x,y;
	END $$
DELIMITER ;		#';'붙이면 에러남

#확인
CALL proc_while();

#LOOP은 : 탈출 조건이 없으면 무한 반복되기 때문에 반복문 내에서 LEAVE문을 사용하여 루프를 종료해야 함
#		이떄 루프에 레이블명을 지정하고, 지정한 레이블명은 LEAVE문에 기술함

#예제10-4
#1부터 10까지의 합을 출력하는 프로시저를 LOOP문으로 작성하시오
DELIMITER $$
CREATE PROCEDURE proc_loop()
	BEGIN
		DECLARE x INT DEFAULT 0;
        DECLARE y INT DEFAULT 0;
        
        loop_sum:LOOP
        SET x = x  + 1;
        SET y = y  + x;
        IF x > 10 THEN
			LEAVE loop_sum;
            END IF;
            SELECT x, y;
		END LOOP;
	END $$
DELIMITER ;

#확인
CALL proc_loop();

#REPEAT: 조건이 참이 될 떄까지 코드를 실행함
#		REPEAT문은 반복문 내에서 조건을 비교하기 전에 최소 한 번은 코드가 실행됨

#예제 10-5
#1부터 10까지의 합을 출력하는 프로시저를 REPEAT문으로 작성하시오
DELIMITER $$
CREATE PROCEDURE proc_repeat()
	BEGIN
		DECLARE x INT DEFAULT 0;
		DECLARE y INT DEFAULT 0;
    
		REPEAT
			SET x = x + 1;
			SET y = y + x;
		UNTIL x >= 10 END REPEAT;
		SELECT x, y;
    END $$
DELIMITER ;
	
#확인
CALL proc_repeat()

/*
SQL 스토어드 프로시저(Stored Procedure)
데이터베이스 객체 중 하나로 데이터베이스에서 수행할 수 있는 이련의 SQL문과 제어문을 저장한 SQL 스토어드 프로그램을 의미함
SQL 스토어드 프로시저를 사용하면 하나의 요청으로 여러 개의 SQL문을 실행할수 있음
데이터 검색이나 조작, 업데이트, 삭제 등과 같은 다양한 작업을 수행할 수 있음
스토어드 프로시저는 시스템 스토어드 프로시저와 사용자 정의 프로시저로 구분 할 수 있음
*/
					    
