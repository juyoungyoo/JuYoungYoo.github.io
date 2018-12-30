---
title: " RestTemplate 사용방법 "
layout: post
date: '2018-12-11 06:43:00'
author: chloe
categories: Spring
tags:
- Spring
- RestTemplate
cover: "/assets/instacode.png"
---

## RestTemplate란?
- Spring 3.0이상 지원
- HTTP 통신에 유용하게 사용되는 템플릿
- Server와의 통신을 단순화하고 restful 원칙을 지킴
- 외 URLConnection, HTTPClient ..


## 구동원리
- org.springframework.http.client
- HTTPClient : HTTP 사용하여 통신하는 범용 라이브러리
- HttpClient를 추상화(HttpEnity를 json,xml)하여 제공
- 내부 통신(Connection) Apache HttpComponents 사용
 <img src="{{ site.baseurl }}/assets/images/resttemplate/1_run.jpg" title="동작원리">

## 기본 세팅
 - spring boot를 기준으로 한다.
 {% highlight javascript %}
 // 라이브러리 추가
 implementation('org.springframework.boot:spring-boot-starter-web')
 // or
 compile group: 'org.springframework', name: 'spring-web', version: '4.2.6.RELEASE'
 compile group: 'com.fasterxml.jackson.core', name: 'jackson-core', version: '2.7.4'
 compile group: 'com.fasterxml.jackson.core', name: 'jackson-databind', version: '2.7.4'
 compile group: 'com.fasterxml.jackson.core', name: 'jackson-annotations', version: '2.7.4'
 {% endhighlight %}
 
{% highlight java %}
 {% raw %}    
 @Bean
 public RestTemplate restTemplate(RestTemplateBuilder builder){
   return builder.build();
 }
 {% endraw %}
 {% endhighlight %}
 
 {% highlight java %}
 HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory();
factory.setReadTimeout(5000); // 읽기시간초과, ms
factory.setConnectTimeout(3000); // 연결시간초과, ms
HttpClient httpClient = HttpClientBuilder.create()
.setMaxConnTotal(100)  // connection pool 적용 : 최대 오픈 connection 수
.setMaxConnPerRoute(5) // connection pool 적용 : IP,Port 당 연결 수 제한
.build();
factory.setHttpClient(httpClient); // 동기실행에 사용될 HttpClient 세팅
RestTemplate restTemplate = new RestTemplate(factory);
 {% endhighlight %}
 
 
  <img src="{{ site.baseurl }}/assets/images/resttemplate/2_method.jpg" title="주 메소드">