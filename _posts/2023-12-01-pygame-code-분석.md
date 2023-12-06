---
layout: single
title:  "[게임 제작기] 2화 코드 재구성"
date:   2023-12-01
excerpt: "객체 지향 구조 만들기"
categories: 
    - pygame
tag: [python, pygame]
toc: true
toc_sticky: true
author_profile: true
sidebar: 
    nav: "docs"
---

이전 포스팅 :: [1화 물고기-사냥-대작전 프로젝트](https://rltgjqmtkdydwk.github.io/pygame/pygame-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8/)
<hr>
<br/><br/><br/>


# 1. 원본 코드 분석
간단한 pygame은 main.py 하나로 실행된다.<br/>
아래는 원래 코드에서 pygame의 기본적인 기능에 대해서 간추린 내용이다.

## 필요한 라이브러리 import 및 화면 초기화
```py
import pygame
import sys

pygame.init()
pygame.display.set_caption('Jumping dino')
MAX_WIDTH = 800  # 게임창 너비
MAX_HEIGHT = 400  # 게임창 높이
```

## main() 구성
### 1. 화면 그려주기
```py
def main():
    screen = pygame.display.set_mode((MAX_WIDTH, MAX_HEIGHT))  # 화면 설정
    fps = pygame.time.Clock()  # 프레임 속도 설정

    # 이미지 로드(화면에서 x, y 좌표 설정)
    imgDino = pygame.image.load('img/dino.png')  # 공룡
    dino_height = imgDino.get_size()[1]
    dino_bottom = MAX_HEIGHT - dino_height
    dino_x = 50
    dino_y = dino_bottom
    jump_top = 200
    leg_swap = True
    is_bottom = True
    is_go_up = False

    imgTree = pygame.image.load('img/tree.png')  # 나무
    tree_height = imgTree.get_size()[1]
    # 이하생략
```

### 2. 게임 진행
```py
    while True:
        screen.fill((255, 255, 255))  # 스크린 채우기

        # 이벤트 체크
        for event in pygame.event.get():
            pass
        
        # 공룡 움직임 처리
        if is_go_up:
            img_y -= 10.0
        elif not is_go_up and not is_bottom:
            img_y += 10.0

        # 공룡의 점프 상단과 하단 체크
        if is_go_up and dino_y <= jump_top:
            is_go_up = False

        # 나무 움직임 처리
        tree_x -= 12.0
            if tree_x <= 0:
                tree_x = MAX_WIDTH
        
        screen.blit(imgTree, (tree_x, tree_y))  # 장애물 그리기

        # 화면 업데이트 및 프레임 속도 조절
        pygame.display.update()
        fps.tick(30)
```

## 게임(=main()) 실행
```py
if __name__ == '__main__':
    main()
```
<br/><br/><br/>

# 2. 코드 문제점
### **1. 게임 같지 않음** <br/>
스크립트 파일이 메인 프로그램이므로 바로 `main()`이 실행된다. 
시작이나 끝이 없어서 게임처럼 보이지 않는다. 

### **2. 유지 보수가 힘듦** <br/>
코드의 가독성이 좋지 않아서 게임의 기능을 수정하거나 추가하고 싶을 때, 전역변수가 잘 정리되어 있지 않아서 찾기 힘들다. 
게임에서 반복되는 상태를 파악하기도 힘들다. 


<br/><br/><br/>


# 3. Refactoring

1. **펭귄 class**
- `class Peng`을 만들어서 내 캐릭터의 동작, 상태 업데이트를 보기 편하게 만들었다.

2. **장애물 class**
- `class Obstacle` 부모 클래스를 만들어서 다른 장애물을 만들 때마다 상속 받아와서 장애물이나 아이템(=물고기)을 쉽게 초기화할 수 있도록 했다.

3. **가독성 문제 해결**
- `main()` 함수에 쓰이는 변수를 `global`로 처리해 필요한 함수에 적절하게 불러와 쓸 수 있도록 정리했다.
-  게임 구성(점수, 배경화면 등)을 각각 함수로 만들어서 가독성을 높였다.

4. **게임 진행 화면 모듈화**
- 원래는 `death_count` 변수를 이용해 if문을 중첩시켜서 어떤 상황에 어떤 화면이 나오는지 알기 힘들었다. 
- `menu()`, `story()`, `died()` 로 나눠서 `class Status`에서 호출하면 상황에 맞는 화면이 나오도록 바꿨다.

![전체 코드 (https://github.com/SKHU-OSS-2023-2/pygame-fish-hunting-adventure/blob/main/play.py)](https://github.com/SKHU-OSS-2023-2/pygame-fish-hunting-adventure/blob/main/play.py)

<br/><br/><br/>







<br/><br/><br/><br/>

---
### 👻 참고한 사이트
- 강의 : [https://youtu.be/wtEhZNdgNuA](https://youtu.be/wtEhZNdgNuA)