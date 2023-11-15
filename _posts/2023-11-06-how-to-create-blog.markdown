---
layout: post
title:  "How to create github.io blog"
date:   2023-11-06 16:27:43 +0900
categories: jekyll update
---

<script src="https://utteranc.es/client.js"
        repo="[ENTER REPO HERE]"
        issue-term="pathname"
        theme="github-dark"
        crossorigin="anonymous"
        async>
</script>

## github에서 저장소 생성
[깃허브 사용자명].github.io 라는 이름으로 repository 생성
<img src="../img/blog1.png">

## 로컬에서 github.io 환경 세팅(mac)
1. ruby 설치
`brew install ruby`
2. Jekyll 및 bundler 설치
`gem install jekyll bundler`
3. 새로운 Jekyll 사이트 생성
`jekyll new [깃허브 사용자명].github.io`
4. 생성된 디렉토리로 이동
`cd [깃허브 사용자명].github.io`

## 깃허브 연동
1. 깃허브 초기화
`git init`
2. 깃허브 주소 연결
`git remote add origin [깃허브 저장소의 주소링크]`