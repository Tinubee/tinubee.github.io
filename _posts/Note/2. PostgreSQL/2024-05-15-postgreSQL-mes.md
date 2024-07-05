---
title: DB PostgreSQL 설정
description: DB를 외부에서 접속할 수 있도록 설정하는 방법
date: 2024-05-15 16:12:00 +0800
categories: [Note,PostgreSQL]
tags: [postgresql]
pin: true
math: true
mermaid: true
#image:
#   path: /commons/devices-mockup.png
#   lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---
1. Telnet 설치(프로그램 기능추가 에서 Telnet 체크하면 자동으로 설치됨. Option사항임)

2. 방화벽 -> 고급 설정 -> 인바운드규칙 -> 새 규칙 클릭해서 새 인바운드 규칙마법사 실행

3. 포트 체크 -> TCP 체크, 특정 로컬포트: 5432 -> 연결허용 -> 도메인,개인,공용 전부 체크 -> 이름: postgreSQL -> 마침버튼 클릭하여 생성완료

**예시로 Username : testuser / Database명 : test 라고 가정.**

4. PostgreSQL 실행하기 -> Login/Group Roles에 testuser 추가(언급 안한 부분은 default)
- General name과 commnet 작성. (name: testuser, comment: for testuser)
- Definition password 설정. (password: testuser)
- Privileges(Can login?: true)

1. Schemas에 guest추가( properties 설정, 언급 안한 부분은 defualt)
- General( name: guest, Owner: postgres, Comment: for testuser)
- Security( Privileges( Grantee: testuser, Privileges: U, Grantor: postgres Grantee: postgres, Privileges: UC, Grantor: postgres ))
- Default privileges( Grantee: testuser, Privileges: r, Grantor: postgres Grantee: postgres, Privileges: rwdaxtD, Grantor: postgres )

1. Guest스키마의 Views에 view 추가(mes랑 연동할 table만큼)
- General(name: v + table명 ex. 원본 public에서 사용하고 있는 테이블명이 inspd일 경우 view명칭은 vinspd, Owner: postgres)
- Code(원하는 SELECT문 작성 ex. SELECT inspd.idwdt, inspd.idnum FROM inspd;)
- Security( Privileges( Grantee: testuser, Privileges: r, Grantor: postgres Grantee: postgres, Privileges: rwdaxtD, Grantor: postgres)

1. public에 있는 Table 중 testuser 읽어갈 수 있도록 해당 테이블에 GRANT SELECT ON (tableName) TO testuser; SQL 문 추가

2. Database 폴더에서 pg_hba.conf 파일 메모장으로 열어서 IPv4 local connections 부분에
TYPE: host, DATABASE: (지정한db명), USER: (생성한user명)  ADDRESS: 0.0.0.0/0, METHOD: password 추가(postgres 유저도 넣으려면 동일한 방식으로 추가해보면 됨!)
ex)

# "local" is for Unix domain socket connections only

| TYPE  | DATABASE | USER | ADDRESS |        METHOD |
| :---- | :------- | :--- | :------ | ------------: |
| local | all      |      |         | scram-sha-256 |

# IPv4 local connections

| TYPE | DATABASE | USER     | ADDRESS      |        METHOD |
| :--- | :------- | :------- | :----------- | ------------: |
| host | all      | all      | 127.0.0.1/32 | scram-sha-256 |
| host | test     | testuser | 0.0.0.0/0    |      password |

# IPv6 local connections

| TYPE | DATABASE | USER | ADDRESS |        METHOD |
| :--- | :------- | :--- | :------ | ------------: |
| host | all      | all  | ::1/128 | scram-sha-256 |

# Allow replication connections from localhost, by a user with the replication privilege

| TYPE  | DATABASE    | USER | ADDRESS      |        METHOD |
| :---- | :---------- | :--- | :----------- | ------------: |
| local | replication | all  |              | scram-sha-256 |
| host  | replication | all  | 127.0.0.1/32 | scram-sha-256 |
| host  | replication | all  | ::1/128      | scram-sha-256 |

**※postgreSQL 종료 후 windows 서비스에서 postgreSQL-x64-15 항목 클릭하여 정지 후 시작버튼 클릭**

---

[postgreSQL깔려있는 경우] 
개발자pc에서 DB정상적으로 가져오고 delete나 update기능 안되는 지 확인하는 방법

1. cmd 열어서 cd C:\Program Files\PostgreSQL\15\bin 입력하여 bin폴더로 이동(dir입력하면 해당 폴더에 있는 파일들 확인 가능, psql --help 입력하면 psql 명령어 사용법 확인 가능)

2. psql -h (비전pc IP, ex. 192.168.1.48) -p 5432 -d (데이터베이스명, ex. test) -U (유저명, ex.  testuser) -W   입력 후 해당 유저에 대한 암호 입력 후 접속(암호는 눈에 보이지 않으므로 그냥 입력 후 엔터 치면 접속. 접속 완료 시 commandline이 db명으로 변경됨)

3. testuser가 접근할 수 있는 db 확인
-> SELECT * FROM public.inspd limit (보고 싶은 데이터 갯수); 입력하여 'public 스키마 접근 권한 없음' 메세지 확인
-> SELECT * FROM guest.vinspd limit (보고 싶은 데이터 갯수); 입력하여 데이터가 정상적으로 출력되는 지 확인

1. 이후 SELECT 를 제외한 다른 권한이 가능한 지 더블 체크

2. \q 입력하여 확인 종료

