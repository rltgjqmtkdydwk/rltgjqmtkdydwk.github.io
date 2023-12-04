---
layout: single
title:  "[게임 제작기] 2화 코드 재구성"
date:   2023-12-01
excerpt: "모듈화 : 객체 지향 구조 만들기"
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
스크립트 파일이 메인 프로그램이므로 바로 main()이 실행된다. 
시작이나 끝이 없어서 게임처럼 보이지 않는다. 

### **2. 유지 보수가 힘듦** <br/>
코드의 가독성이 좋지 않아서 게임의 기능을 수정하거나 추가하고 싶을 때, 전역변수가 잘 정리되어 있지 않아서 찾기 힘들다. 
게임에서 반복되는 상태를 파악하기도 힘들다. 


<br/><br/><br/>


# 3. Refactoring

1. **class 모듈화** <br/> - 게임 캐릭터를 class로 만들어서 동작, 상태 업데이트를 보기 편하게 만들었다.
2. **class 모듈화2** <br/> - 장애물 하나를 부모 class로 만들어 다른 장애물과 아이템(=물고기)을 초기화할 수 있도록 했다.
3. **가독성 문제 해결** <br/> - main 함수에 쓰이는 변수를 한데모아 정리했다. 또한 게임 구성(점수, 배경화면 등)을 내부 함수로 만들어서 가독성을 높였다.

<br/><br/><br/>

```py
import pygame
import random
import sys
pygame.init()

SCREEN_WIDTH = 1100
SCREEN_HEIGHT = 500

SCREEN = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
STORY_SCREEN = pygame.image.load('img/story_screen.jpg')
START_BACKGROUND = pygame.image.load("./img/start_menu.jpg")
DEATH_SCREEN = pygame.image.load("./img/game_over.jpg")

RUNNING = [pygame.image.load("./img/peng_run_1.png"),
           pygame.image.load("./img/peng_run_2.png")]
JUMPING = pygame.image.load("./img/peng_jump.png")
DUCKING = pygame.image.load("./img/peng_duck.png")

SEAL = pygame.image.load("./img/seal.png")
FISH = pygame.image.load("./img/fish.png")

BG = pygame.image.load("./img/main_background.png")
SOUND = pygame.mixer.Sound('sound/bgm.ogg')


class Peng:
    X_POS = 80
    Y_POS = 350
    Y_POS_DUCK = 370
    JUMP_VEL = 8

    def __init__(self):
        self.duck_img = DUCKING
        self.run_img = RUNNING
        self.jump_img = JUMPING

        self.peng_duck = False
        self.peng_run = True
        self.peng_jump = False

        self.step_index = 0
        self.jump_vel = self.JUMP_VEL
        self.image = self.run_img[0]
        self.peng_rect = self.image.get_rect()
        self.peng_rect.x = self.X_POS
        self.peng_rect.y = self.Y_POS

    def update(self, userInput):
        if self.peng_duck:
            self.duck()
        if self.peng_run:
            self.run()
        if self.peng_jump:
            self.jump()

        if self.step_index >= 10:
            self.step_index = 0

        if userInput[pygame.K_UP] and not self.peng_jump:
            self.peng_duck = False
            self.peng_run = False
            self.peng_jump = True
        elif userInput[pygame.K_DOWN] and not self.peng_jump:
            self.peng_duck = True
            self.peng_run = False
            self.peng_jump = False
        elif not (self.peng_jump or userInput[pygame.K_DOWN]):
            self.peng_duck = False
            self.peng_run = True
            self.peng_jump = False

    def duck(self):
        self.image = self.duck_img
        self.peng_rect = self.image.get_rect()
        self.peng_rect.x = self.X_POS
        self.peng_rect.y = self.Y_POS_DUCK

    def run(self):
        self.image = self.run_img[self.step_index // 5]
        self.peng_rect = self.image.get_rect()
        self.peng_rect.x = self.X_POS
        self.peng_rect.y = self.Y_POS
        self.step_index += 1

    def jump(self):
        self.image = self.jump_img
        if self.peng_jump:
            self.peng_rect.y -= self.jump_vel * 4
            self.jump_vel -= 0.8
        if self.jump_vel < - self.JUMP_VEL:
            self.peng_jump = False
            self.jump_vel = self.JUMP_VEL

    def draw(self, SCREEN):
        SCREEN.blit(self.image, (self.peng_rect.x, self.peng_rect.y))

class Obstacle:
    def __init__(self, image):
        self.image = image
        self.rect = self.image.get_rect()
        self.rect.x = SCREEN_WIDTH

    def update(self):
        self.rect.x -= game_speed
        if self.rect.x < -self.rect.width:
            obstacles.pop()

    def draw(self, SCREEN):
        SCREEN.blit(self.image, self.rect)

class Seal(Obstacle):
    def __init__(self, image):
        super().__init__(image)
        self.rect.y = 350

class SeaGull(Obstacle):
    def __init__(self, image):
        super().__init__(image[0])
        self.image_list = image
        self.rect.y = 280
        self.index = 0
    
    def draw(self, SCREEN):
        if self.index >= 9:
            self.index = 0
        flapping = self.image_list[self.index // 5]
        flapping_rect = flapping.get_rect(topleft=(self.rect.x, self.rect.y))
        SCREEN.blit(flapping, flapping_rect)
        self.index += 1

class Fish(Obstacle):
    def __init__(self, image):
        super().__init__(image)
        y_pos_fish = [170, 350]
        self.rect.y = random.choice(y_pos_fish)
        self.index = 0


def main():
    global game_speed, x_pos_bg, y_pos_bg, points, obstacles, current_bg
    run = True
    clock = pygame.time.Clock()
    player = Peng()
    game_speed = 20
    x_pos_bg = 0
    y_pos_bg = 0
    points = 0
    scores = 0
    font = pygame.font.Font('freesansbold.ttf', 20)
    obstacles = []
    death_count = 0
    current_bg = 0 # 현재 배경 추적 변수
    pygame.mixer.music.load('sound/bgm.ogg')
    pygame.mixer.music.play(-1)
    # START_SOUND.play(-1)

    def score():
        global points, game_speed
        points += 1
        if points % 150 == 0:
            game_speed += 0.5

        textPoint = font.render("Points: " + str(points), True, (0, 0, 0))
        textScore = font.render("Score: " + str(scores), True, (0, 0, 0))
        textPointRect = textPoint.get_rect()
        textScoreRect = textScore.get_rect()
        textPointRect.center = (1000, 40)
        textScoreRect.center = (1000, 80)
        SCREEN.blit(textPoint, textPointRect)
        SCREEN.blit(textScore, textScoreRect)

    def background():
        global x_pos_bg, y_pos_bg, current_bg
        image_width = BG.get_width()

        if current_bg == 0:
            SCREEN.blit(BG, (x_pos_bg, y_pos_bg))
            SCREEN.blit(BG, (image_width + x_pos_bg, y_pos_bg))
        else:
            darkbg = BGDARK[current_bg - 1]
            SCREEN.blit(darkbg, (x_pos_bg, y_pos_bg))
            SCREEN.blit(darkbg, (image_width + x_pos_bg, y_pos_bg))
        
        if x_pos_bg <= -image_width:
            x_pos_bg = 0
        x_pos_bg -= game_speed - 17

        global points
        if points % 300 == 0 and points != 0:
            current_bg = (current_bg + 1) % (len(BGDARK) + 1)

    while run:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                run = False
                pygame.quit()
                sys.exit()

        userInput = pygame.key.get_pressed()

        background()
        player.draw(SCREEN)
        player.update(userInput)

        if len(obstacles) == 0:
            if random.randint(0, 30) <= 9:
                obstacles.append(Seal(SEAL))
            elif random.randint(0, 30) <= 14:
                obstacles.append(SeaGull(SEAGULL))
            elif random.randint(0, 30) <= 29:
                obstacles.append(Fish(FISH))

        for obstacle in obstacles:
            obstacle.draw(SCREEN)
            obstacle.update()
            if player.peng_rect.colliderect(obstacle.rect):
                if(obstacle.image == FISH):
                    obstacles.remove(obstacle)
                    scores += 1
                    COIN_SOUND.play()
                else:
                    pygame.mixer.music.stop()
                    pygame.time.delay(1000)
                    death_count += 1
                    menu(death_count)

        score()

        clock.tick(30)
        pygame.display.update()
    pygame.mixer.music.stop()


def menu(death_count):
    global points
    run = True 
    if death_count > 0:
        # START_SOUND.stop()
        MISS_SOUND.play()
        SCREEN.blit(DEATH_SCREEN,(0,0)) 
        pygame.display.update()
        waiting_for_key = True
        while waiting_for_key:
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    pygame.quit()
                    sys.exit()
                elif event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_r:
                        # r 키를 누르면 처음 메인 화면으로 돌아감
                        waiting_for_key = False
                        main()
    elif death_count == 0:
        while run:
            SCREEN.blit(START_BACKGROUND,(0,0))
            pygame.display.update()
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    run = False
                    sys.exit()
                elif event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_y:
                        SCREEN.blit(STORY_SCREEN,(0,0))
                        pygame.display.update()
                        waiting_for_key = True
                        while waiting_for_key:
                                for event in pygame.event.get():
                                    if event.type == pygame.QUIT:
                                        pygame.quit()
                                        sys.exit()
                                    elif event.type == pygame.KEYDOWN:
                                        if event.key == pygame.K_s:
                                            main()
            

menu(death_count=0)
```






<br/><br/><br/><br/>

---
### 👻 참고한 사이트
- 강의 : [https://youtu.be/wtEhZNdgNuA](https://youtu.be/wtEhZNdgNuA)