---
layout: single
title:  "[블로그 제작기] Google Analytics 방문자 통계 만들기
date:   2023-11-11
excerpt: "깃허브 블로그에 방문한 사람 수 세기"
categories: 
    - blog
tag: [github, blog, minimal-mistakes]
toc: true
toc_sticky: true
author_profile: true
sidebar: 
    nav: "docs"
---

# 1. 구글 애널리틱스 사이트에 로그인
<a href="https://marketingplatform.google.com/about/analytics/">구글 애널리틱스 사이트</a>
<br/>
자신의 구글 계정으로 로그인한 후, 깃허브 블로그 주소를 써넣는다. (기업의 규모 같은 비즈니스적인 내용은 대충 적는다. 어차피 블로그라 상관없을 듯 싶다)
<br/><br/>

# 2. _config.yml 수정
**_config.yml** 에서 # Analytics 부분의 provider:와 tracking_id을 수정한다. 
```yml
# Analytics
analytics:
  provider               : "google-gtag" # false (default), "google", "google-universal", "google-gtag", "custom"
  google:
    tracking_id          : G-9E4ENXQZVY     # 자신이 할당받은 트레킹 아이디로 쓸 것
    anonymize_ip         : # true, false (default)
```
<br/><br/>

# 3. google tag 코드 추가
**_includes/head.html** 의 중간에 gtag 스크립트 코드를 추가해준다.
```html
<!--생략-->
</noscript>

<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-9E4ENXQZVY"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-9E4ENXQZVY');
</script>

{% if site.head_scripts %}
  <!--생략-->
```
<br/><br/>

# 4. github에 push
깃허브에 push 해주고 조금만 기다리면 애널리틱스 홈페이지에서 내 블로그의 방문자 통계를 볼 수 있다.
<img src="/assets/analytics1.png">