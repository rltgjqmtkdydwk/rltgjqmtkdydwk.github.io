---
layout: single
title:  "[블로그 제작기] utterance 댓글 기능 구현하기"
date:   2023-11-08
excerpt: "깃허브 블로그에 댓글창 만들기"
categories: 
    - blog
tag: [github, blog, minimal-mistakes]
toc: true
toc_sticky: true
author_profile: true
sidebar: 
    nav: "docs"
---

utterance를 사용해서 댓글 기능을 구현하면, 내 깃허브 repo에서 이슈로 댓글 상태를 알 수 있다.
<br/><br/><br/><br/>

# 1. utterance 설정
<a href="https://github.com/apps/utterances">utterances 깃허브 앱</a><br/>
여기에서 Install을 누르면 사진처럼 Configure로 설정된다. 
<img src="/assets/img/utterances1.png">

# 2. utterance script 생성
<a href="https://utteranc.es/">utterances 웹사이트</a><br/>
그러고 나면 이런 웹페이지가 뜬다. 여기에서 우린 github.io repo에만 댓글기능을 넣고 싶기 때문에 'Only select repo'로 선택하면 다음 화면으로 넘어간다.<br/>
<img src="/assets/img/utterances2.png">
그럼 사진에 나온 repo: owner/repo 칸에 utterance와 연결할 repo를 써주면 된다. 
이 부분이 중요한데, 여기에 쓰는 것에 따라 아래 utterance script에 반영되기 때문이다. 이 코드는 보통 **layouts/post.html**에 복붙해서 쓴다. 
<img src="/assets/img/utterances3.png">
<br/>
그러나!!! minimal-mistakes 테마에서는 그럴 필요가 없다. 이미 **_includes/comments-providers/utterances.html** 파일이 있기 때문이다. 왜 그런건지는 직접 파일을 확인해보시라.

# 3. _config.yml 수정
provider:, utterances:, comments: 부분을 수정해준다. 
```yml
comments:
  provider               : "utterances"
                            # false (default), "disqus", "discourse", "facebook", "staticman", "staticman_v2", "utterances", "giscus", "custom"
  disqus:
    <!-- 생략 -->
  utterances:
    theme                : "github-light" 
                            # "github-light" (default), "github-dark"
    issue_term           : "pathname" 
                            # "pathname" (default)
  giscus:
    <!-- 생략 -->
# Defaults
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      read_time: true
      comments: true 
                # true
      share: true
      related: true  
```
<br/>

# 4. 근데 나는 왜 댓글이 안 생길까?