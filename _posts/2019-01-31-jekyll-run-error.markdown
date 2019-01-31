---
layout: post
title: " Jekyll(지킬) ERROR "
#subtitle : "[ERROR] Invalid CP949 character "\xE2" on line 155 .."
categories: jekyll
tags: jekyll
comments: true
menu: false
order: 1
---
## Jekyll 구동 시 Invalid CP949 character "\xE2" on line 155 .. 에러 해결 방안
- 새로운 플러그인이나 기능 추가, 테마를 받아 실행할 때 가끔 발생하며 SASS를 사용하는 패키지 인코딩 문제입니다.

## -해결방안
```
chcp 65001
bundle exec jekyll serve
```
<img src="{{ site.baseurl }}/assets/images/20190131/success.jpg" title="success_img">

[git server example download]:   https://github.com/JuYoungYoo/spring-rest-api
[git client example download]:   https://github.com/JuYoungYoo/client-server
