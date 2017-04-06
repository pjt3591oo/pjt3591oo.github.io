---
layout: post
title:  "[news] Django 1.11 Release"
date:   2017-04-06 08:03:05 +0900
categories: news
tags: [programming]
published: true
writer: pjt3591oo
count: 125
---

April 4, 2017 Django realse notes 일부

## 호환성

Django 1.11은 파이썬 **2.7, 3.4, 3.5, 3.6**에서 작동합니다. 3.6을 지원하는 첫 Django 버전입니다. 각 버전별 마지막 릴리스를 사용하기를 추천합니다.

Django 1.11.x는 파이썬 2를 지원하는 마지막 버전입니다. 다음 번 메이저 릴리스인 Django 2.0부터는 파이썬 3.5 이상만 지원할 예정입니다.


## 새 기능

### 1. index 클래스

### 2. 템플릿 기반의 폼 위젯 렌더링

### 3. 하위 쿼리 표현식

### 4. 그 외의 추가된 기능들
```
- CSRF-USE_SESIONS을 설정하여 CSRF 토큰을 쿠기가 아닌 세에 저장가능(default : False)

- PostgreSQL에서 QuerySEt.iterator()메서드가 서버측 커서를 사용하여 서버 메모리 부담을 데이터베이스 메모리로 전환가능

- Form에서 get_initial_for_field() 메서드를 사용하 필드 초기값 지정가능

- loaddata 명령에 --exclude 옶션추

- showmigrations 명령에서 --plan옵 사용할 때 app_label 지정가능

- get_or_create(), update_or_create() 메서드에서 defaults의 값으로 함수 값(callable value)지정 가능

- DateTime, TimeField에 대해 Trunc 클래스 적용 시 특정 부분 이하를 자를 수 있음

- 쿼리셋의 values() 메서드와 values_list() 메서드에서도 표현식 사용 가능

- 쿼리 표현식에서 정렬시 null값의 위치 조정가능

- 두 쿼리셋의 교집합과 합집합 차집합을 만들 수 있는 union(), intersection(), difference()메서드 추가
```

### 바뀐 점
```
- collectstatic 실행 시 파일 간 순환 참조가 존재하면 오류 발생

- DateTimeField에 대해 상세 룩업지원

- PostgreSQL 9.2와 PostGIS 지원중단

- get_or_create()와 update_or_create()에서 변수명 검사

- pytz 패키지 의존성이 추가되었고, TIME_ZONE = None옵션이 사라짐

```

### 사소한 변경
```
- 테스트를 실행할 떄 ALLOWED_HOSTS를 검사

- 모델 폼에서 CharField가 null=true 인 경우 빈 문자열 대신 null 저장

- 체크박스와 셀렉트박스 폼을 렌더링할 때 checked='checked', selected='selected' 대신 checked, selected만 넣는다.
```

### 삭제된 기능
```
- models.permalink 데코레이터

- contrib.auth의 각종 함수 기반 뷰
```

