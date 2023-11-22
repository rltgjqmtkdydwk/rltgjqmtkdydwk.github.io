---
layout: single
title:  "[블로그 제작기] 깃허브 블로그 만들기"
date:   2023-11-06 16:27:43 +0900
excerpt: "깃허브 블로그 만들기"
categories: 
    - blog
tag: [github, blog, ruby, jekyll]
toc: true
toc_sticky: true
author_profile: true
sidebar: 
    nav: "docs"
---

깃허브에서 블로그를 만들 수 있다! 이걸로 개발 블로그는 끝장났다.
<br/><br/><br/><br/>

# 1. github에서 저장소 생성
[깃허브 사용자명].github.io 라는 이름으로 repository 생성
![repository 이름 설정 화면](/assets/img/blog1.png)

# 2. 로컬에서 github.io 환경 세팅(mac)
1. ruby 설치
```sh
$ brew install ruby
```
2. Jekyll 및 bundler 설치
```sh
$ gem install jekyll bundler
```
3. 새로운 Jekyll 사이트 생성
```sh
$ jekyll new [깃허브 사용자명].github.io
```
4. 생성된 디렉토리로 이동
```sh
$ cd [깃허브 사용자명].github.io
```

디렉토리를 확인해보면 다음과 같은 항목들이 생겼을 것이다.
![repository 이름 설정 화면](/assets/img/blog2.png)

# 3. 깃허브 연동
1. 깃허브 초기화
```sh
$ git init
```
2. 깃허브 주소 연결
```sh
$ git remote add origin [깃허브 저장소의 주소링크]
```