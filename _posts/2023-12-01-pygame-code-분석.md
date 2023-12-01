---
layout: single
title:  "[게임 제작기] 2화 물고기 사냥 대작전"
date:   2023-12-01
excerpt: "공룡게임을 기반으로 게임 만들기"
categories: 
    - pygame
tag: [python, pygame]
toc: true
toc_sticky: true
author_profile: true
sidebar: 
    nav: "docs"
---

이전 포스팅 :: [1화 물고기-사냥-대작전 프로젝트](_posts/2023-11-20-pygame-프로젝트.md)
<hr>


<br/><br/><br/>

# 1. 코드 분석
간단한 pygame은 main.py 하나로 실행된다.<br/>
아래는 원래 코드에서 pygame을
```py
import ~ # 필요한 라이브러리(pygame, sys) import

# pygame 초기화, 게임 창(너비, 높이) 초기화
pygame.init()
pygame.display.set_caption('Jumping dino')
MAX_WIDTH = 800
MAX_HEIGHT = 400

screen = pygame.display.set_mode((MAX_WIDTH, MAX_HEIGHT)) # 화면 설정
fps = pygame.time.Clock() # 프레임 속도 설정

# 공룡 이미지 로드(캐릭터(x, y), 장애물(x, y))
imgDino = pygame.image.load('dino_image.png')
dino_height = imgDino.get_size()[1]
dino_bottom = 창 높이 - imgDino_height

# 게임 실행
while True:
    screen.fill((255, 255, 255)) # 스크린 채우기

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
    
    screen.blit(imgTree, (tree_x, tree_y)) # 장애물 그리기

    # 화면 업데이트 및 프레임 속도 조절
    pygame.display.update()
    fps.tick(30)

if __name__ == '__main__':
    main()
```








### 👻 참고한 사이트
간단한 pygame을 만든 깃허브를 찾아보던 중에 아래 블로그를 찾았다. 이 외에 참고할 만한 깃허브나 유튜브 강의가 많아서 pygame을 입문할 때 좋은 게임이 될 것이다.
- 블로그 : [파이썬 공룡 게임 만들기 - 개발자 지망생](https://blockdmask.tistory.com/419)
    - 깃허브 주소 [Python_dinosaur_game](https://github.com/BlockDMask/Python_dinosaur_game.git)
    - 유튜브 링크 [make python game - dinosaur jump](https://youtu.be/ok_8mvQ8CiY)
- 강의 : [파이썬 게임개발 특강 | 크롬공룡게임 - 코딩엑스AI](https://youtu.be/wtEhZNdgNuA)