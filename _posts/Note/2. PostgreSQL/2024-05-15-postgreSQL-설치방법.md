---
title: DB 설치하기
description: DB 설치 및 환경설정
date: 2024-05-15 15:12:00 +0800
categories: [Note,PostgreSQL]
tags: [postgresql]
pin: true
math: true
mermaid: true
published: false
#image:
#   path: /commons/devices-mockup.png
#   lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---

### DB 설치 순서.

1. Database설치 경로에 Database 폴더 생성. <!--C:\IVM\Database 폴더 생성-->

2. PostgreSQL 설치
(프로그램 경로는 디폴트, 설치 섹션은 Builder빼고 전부, Data폴더는 위에 생성한 Database로 지정, port도 디폴트인 5432)

3. id와 비밀번호 설정하기. <!--ivmadmin으로 통일 -->

4. postgreSQL 실행시켜서 프로젝트명으로 database 생성

5. 하위 public 스키마에서 table들을 생성(이름은 그때 필요한 테이블명으로 지정)

6. 하위 Column들 설정 및 Comment 설정