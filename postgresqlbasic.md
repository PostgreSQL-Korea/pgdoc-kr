# 3.PostgreSQL 기본 개념

## 3.1 PostgreSQL 객체

### 3.1.1 테이블

1\) 정의

관계형 데이터 베이스를 구성하는 기본 데이터 구조로서 행과 컬럼\(열\)의 구조를 가지는 저장공간이며 각 컬럼은 데이터 타입을 가지고 있다.

2\) 기본값

데이터 값을 입력하지 않는 경우 자동으로 입력되는 기본값을 말한다.

```
CREATE TABLE user_info (
    user_id       varchar(32)    not null,
    sso_use_yn    char(1)        default 'N'
);
```

3\) 제약조건

입력하는 데이터에 대한 제약, 즉 데이터 무결성을 지키기 위한 조건을 의미한다.

가. CHECK 제약 조건

* 컬럼의 값이 Boolean\(진리값\) 표현식을 만족해야 함.
* 제약 조건에 개별 이름을 부여할 수 있으며 CONSTRAINT 뒤에 식별자, 그 뒤에 제약 조건을 정의

```
CREATE TABLE user_info (
    user_id       varchar(32)     not null,
    age           smallint        CHECK(age > 0),
    height        smallint        CONSTRAINT    positive_height    CHECK (height > 0)
);
```

나. NOT NULL 제약 조건

* 컬럼 값이 null 값이 아님을 체크

```
CREATE TABLE user_info (
    user_id varchar(32) not null
);
```

다. UNIQUE 제약 조건

* 컬럼 또는 컬럼 그룹에 포함된 데이터가 테이블의 모든 행에서 중복되는 데이터가 없어야 함
* 컬럼 또는 컬럼 그룹에 자동으로 고유 btree 인덱스가 생성됨
* null 값은 중복 저장 허용

```
* 컬럼에 제약 조건을 사용하는 경우
CREATE TABLE user_info (
    user_id varchar(32) UNIQUE
);

* 테이블에 제약 조건을 사용하는 경우
CREATE TABLE user_info (
    user_id     varchar(32),
    user_name   varchar(60),
    UNIQUE(user_id)
);

* 컬럼 그룹에 제약 조건을 사용하는 경우
CREATE TABLE user_info (
    user_id          varchar(32),
    user_name        varchar(60),
    social_number    int,
    UNIQUE(user_name, social_number)
);
```

라. 기본 키

* 일반적으로 테이블의 각 행은 고유하게 식별되는 값을 가진 컬럼 또는 컬럼의 조합을 가지고 있으며 이를 기본키라 한다.
* 기본키\(PRIMARY KEY\) 제약 조건은 UNIQUE 제약 조건과 NOT NULL 제약 조건의 조합
* 테이블은 보통 1개의 기본키를 가질 수 있음

```
CREATE TABLE user_info (
    user_id                  varchar(32),
    PRIMARY KEY(user_id)
);
```

바. 외래 키

* 두 테이블 간의 관계를 설정하고 강제 적용하여 외래 키 테이블에 저장 되는 데이터를 제어 하는데 사용 됨
* 한 테이블이 기본 키 값을 가지고 있는 컬럼을 다른 테이블의 컬럼이 참조할 때 두 테이블 간의 관계가 생성되고 이때 두번째 테이블에 추가되는 컬럼을 외래키라고 함.
* 1개 이상의 외래 키를 가질 수 있으며 참조되는 컬럼의 수 및 타입은 일치해야 함
* 제한 및 케스케이딩 삭제는 두가지 가장 일반적인 옵션이며 RESTICT 는 참조되는 행의 삭제를 금지하며 CASCADE는 참조되는 행이 삭제될 경우 참조하는 행도 자동으로 삭제되는지를 지정함

```
* 게시물을 저장하는 board 와 게시물 comment 저장하는 board_comment 테이블
CREATE TABLE board (
    board_id             bigint,
    title                varchar(256)    NOT NULL,
    PRIMARY KEY(board_id)
);

CREATE TABLE board_comment (
    board_comment_id    bigint,
    board_id            bigint,
    contents            text            NOT NULL,
    .... 생략 ....
    FOREIGN KEY(board_id) REFERENCES board(board_id)
);

* RESTICT 옵션
CREATE TABLE board_comment (
    board_comment_id    bigint,
    board_id            bigint    REFERENCES    board ON DELETE RESTRICT
    ... 생략...
);

* CASCADE 옵션
CREATE TABLE board_comment (
    board_comment_id    bigint,
    board_id            bigint    REFERENCES    board ON DELETE CASCADE
    ... 생략...
);
```

사. 제외 제약 조건

### 3.1.2 트리거

### 3.1.3 Rule

## 3.2 PostgreSQL의 데이터 타입

## 3.3 함수와 연산자

## 3.4 타입 변환

## 3.5 인덱스

## 3.6 Vaccum과 공간관리 - 이건 엑셈에서 하기로 했음



