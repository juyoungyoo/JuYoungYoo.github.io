---
title: " Spring, RestAPI 구현(CRUD)"
subtitle : RestAPI 구현 및 설계 방법과 주의해야할 점
layout: post
#date: '2018-12-11 05:43:00'
#author: chloe
categories: devlog
tags: spring
comments: true
# cover: "/assets/instacode.png"
---
## Spring + RestAPI 구현(CRUD) 그리고 RESTAPI 설계 방법과 주의해야할 점 RestTemplate에 대해서

### 참고 소스
- [rest api server단 예제][git server example download]
- [rest api client단 예제][git client example download]


## RestAPi란?

- REST ( REpresentational State Transfer )
- 웹(HTTP) 장점을 최대한 활용할 수 있는 아키텍처
- 오픈 진영(Goolgle, Amazon)에 의해 주도

## REST 기본구성
 <img src="{{ site.baseurl }}/assets/images/restful/1_configure.jpg" title="REST 기본구성">


## RESTFUL API 6가지 특징
1. Uniform (유니폼 인터페이스)
URI 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍처 스타일(HTTP/JSON, HTTP XML .. )
		'HTTP 표준에 맞게 개발했을 경우, Android, ios, python 특정 언어에 기술에 종속 받지 않고 모든 플랫폼에서 사용가능'
 2. Cacheable (캐시 가능)
  HTTP 기존 웹표준을 그대로 사용. 기존 웹 인프라 그대로 사용함으로 써 THTTP 캐싱 기능 사용 ( 사용법 : Last-Modified, E-tag 이용 )
 3. Stateless (무상태성)
    상태정보를 따로 저장,관리 하지 않는다.( 세션, 쿠키 정보 X )
    서비스 자유도가 높아짐 ( 단순 구현 )
    즉, 단순히 요청을 처리한다.
 4. Self-descriptiveness (자체 표현 구조)
   REST API 메세지만을 보고 쉽게 이해 가능하도록 구현
 5. Client - Server 구조
        REST 서버 -> API 제공
        Client 서버 -> 사용자 인증, 컨텍스트(세션,로그인 정보)
        ! 서버, 클라이언트 개발 내용 명확해지며 서로 의존성 저하
  6. 계층형 구조
     다중 계층으로 구성 가능 ( 보안, 로드밸런싱, 암호화 계층 추가) + proxy, gateway 사용가능
      구조의 유연성

			! RDBMS 관계형 리소스 표현하기 어려움. REST한 테이블 구조 설계 /
			! JSON을 그대로 저장하는 도큐먼트 기반의 NoSQL에 적합


## REST API 규칙, 원칙
-  URI는 모든 개체를 '리소스'단위로만 표현만 하며, 행위는 HTTP Method로 표현한다.
-  리소스는 (복수)명사
 <img src="{{ site.baseurl }}/assets/images/restful/2_example.jpg" title="예제">

###  URI 설계 주의할 점
  - 경로는 모두 소문자
  - 특수문자는 하이푼(-)만 사용 ( _ 사용 안함 )
  - 슬래시 구분자(/) 계층 관계를 나타냄
      ex ) http://api.example.com/house/apartments
  - URI 마지막 문자는 슬래시(/)를 포함하지 않는다.
  - 확장자는 URI에 포함하지 않는다. accpet header 이용
    [ Accept header 이용 : GET /boards/1 Accept:image/jpg ]

### resource 관련 표현
 - 모든 개체 리소스로 표현
<img src="{{ site.baseurl }}/assets/images/restful/3_example.jpg" title="예제">

	'간단한 구현일 경우 option 1 사용. 이 외 option2 권장'

## HATEOAS

 - Hypermedia As The Engine Of Application State
 -  애플리케이션 엔진의 상태, 정보를 하이퍼미디어로 표현
 <img src="{{ site.baseurl }}/assets/images/restful/4_hateoas.jpg" title="HATEOAS">


## 에러처리 HTTP Response Code

  - 권장 Response Code  : 200, 400, 401, 404, 500
  - Error Stack은 response 메세지에 포함시키지 않는다.

{% highlight json %}
	{
 "error"          : 20003,
 "message"  : "Autorization failed",
 "detail"         : "User doesn't have permission to update user profile.",
 "mereinfo" : http://myapi.com/docs/20003
	}
{% endhighlight %}

에러처리 Reponse body example
* Error code 범위를 미리 정해놓는게 좋은 2000번대 인증 관련, 30000번대 주문 관련
* Error code 별 메세지와, Detail등은 별도의 파일로 관리하는 것이 다국어 지원에 유리함




### [참고] 상황별 REST Ful API 구현 방법

1. 버전관리  (API 버전 정의 방법)
  - faceobook : ?v=2.0
  - api.server.com/{서비스명}/{버전}/{리소스}
     ex) api.server.com/account/v2.0/groups

2. Filtering
  - 전체 필드 요청 또는 filtering 지원 한다.
<img src="{{ site.baseurl }}/assets/images/restful/5_example.jpg" title="5example">

3. Sorting
  - 오름차순/내림차순 소팅이 여러 Field에 대해 되도록 구현
  -  [ GET /cars?sort=-username, +country]  // - 내림차순, +오름차순

4. Field selection
   - 모바일 client에서는 요청하는 속성이 적음으로 필요한 속성만을 처리하는 API 구현 필요
   - 네트워크 전송량, API 사용 속도 성능 향상
   - [ GET /cars?fields=username, id, contry, address]
	<img src="{{ site.baseurl }}/assets/images/restful/6_company_example.jpg" title="6">

7. Paging
  - 많은 데이터 리턴 시 잘라서 리턴하는 페이징 처리 필요
<img src="{{ site.baseurl }}/assets/images/restful/7_company_example.jpg" title="7">

8.Search
  - HTTP GET 쿼리 스트링 사용
 <img src="{{ site.baseurl }}/assets/images/restful/8_company_example.jpg" title="8">

 9. HATEOS (Hpermeia As The Engine Of Application State )
 - HTML Link 다른 리소스, 연관된 행위에 대한 링크를 제공함으로써, Self-descriptiveneses 극대화
  - 의존성 ↑
<img src="{{ site.baseurl }}/assets/images/restful/9_hateos.jpg" title="9">


[git server example download]:   https://github.com/JuYoungYoo/spring-rest-api
[git client example download]:   https://github.com/JuYoungYoo/client-server
