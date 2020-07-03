# 2jh1230
스페이스바 = 공격
방향키 = 이동
게임실행 후 떨어지는 운석을 공격하여 파괴하면 됨
```python
import pygame
import sys
import random
from time import sleep

BLACK = (0, 0, 0)
garo = 480
sero = 640
rockimage = ['rock.png']

def fail(text):
    global gamePad
    textfont = pygame.font.Font('NanumGothic.ttf', 80)
    text = textfont.render(text,True, (255,0,0))
    textpos = text.get_rect()
    textpos.center = (garo/2, sero/2)
    gamePad.blit(text, textpos)
    pygame.display.update()
    sleep(2)

def crash():
    global gamePad
    fail('전투기 파괴') 

def gameover():
    global gamePad
    fail('게임 오버')
    again=int(input('다시 하시려면 1를 눌러주세요'))

def score(count):
    global gamePad
    font = pygame.font.Font('NanumGothic.ttf', 20)
    text = font.render('점수 : '+ str(count), True, (255,255,255))
    gamePad.blit(text, (10,0))

def passed1(count):
    global gamePad
    font = pygame.font.Font('NanumGothic.ttf', 20)
    text = font.render('음의 점수 : ' + str(count), True, (255, 0, 0))
    gamePad.blit(text, (360, 0))

def unit(unit, x, y):
    global gamePad
    gamePad.blit(unit, (x, y))

def Game():
    global gamePad, clock, background, fighterjet, missile, explore, ufo
    pygame.init()
    gamePad = pygame.display.set_mode((garo, sero))
    pygame.display.set_caption('슈팅게임')
    background = pygame.image.load('background.jpg')
    fighterjet = pygame.image.load('fighterjet2.png')
    missile = pygame.image.load('missile.png')
    explore = pygame.image.load('explore.png')
    ufo = pygame.image.load('ufo.png')
    clock = pygame.time.Clock()

def Game1():
    global gamePad, clock, background, fighterjet, missile, explore, ufo

    fighterjetsize = fighterjet.get_rect().size
    fighterjetgaro = fighterjetsize[0]
    fighterjetsero = fighterjetsize[1]
    
    missileGS = []
    x = garo * 0.45
    y = sero * 0.9
    fighterjetX = 0
    fighterjetY = 0

    ufosize = ufo.get_rect().size
    ufogaro = ufosize[0]
    ufosero = ufosize[1]

    ufoX = garo * 0.05
    ufoY = sero * 0.05
    A = garo * 0.05
    B = sero * 0.4

    rock = pygame.image.load('rock.png')
    rocksize = rock.get_rect().size
    rockgaro = rocksize[0]
    rocksero = rocksize[1]

    rockG = random.randrange(0, garo - rockgaro)
    rockS = 0
    rockspeed = 2

    success = False
    count1 = 0
    passed = 0

    exitGame = False
    while not exitGame:
        for event in pygame.event.get():
            if event.type in [pygame.QUIT]:
                pygame.quit()
                sys.exit()

            if event.type in [pygame.KEYDOWN]:
                if event.key == pygame.K_LEFT:
                    fighterjetX -= 5
                elif event.key == pygame.K_RIGHT:
                    fighterjetX += 5
                elif event.key == pygame.K_UP:
                    fighterjetY -= 5
                elif event.key == pygame.K_DOWN:
                    fighterjetY += 5
            if event.type in [pygame.KEYUP]:
                if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                    fighterjetX = 0
                if event.key == pygame.K_UP or event.key == pygame.K_DOWN:
                    fighterjetY = 0
                elif event.key == pygame.K_SPACE:
                    missileG = x + fighterjetgaro/2
                    missileS = y - fighterjetsero
                    missileGS.append([missileG, missileS])

        unit(background, 0, 0)
        unit(fighterjet, x, y)

        if len(missileGS) != 0:
            for i, bGS in enumerate(missileGS):
                bGS[1] -= 10
                missileGS[i][1] = bGS[1]

                if bGS[1] < rockS:
                    if bGS[0] > rockG and bGS[0] < rockG + rockgaro:
                        missileGS.remove(bGS)
                        success = True
                        count1 += 1

            if bGS[1] <= 0:
                try:
                    missileGS.remove(bGS)
                except:
                    pass
        if len(missileGS) !=0:
            for bG, bS in missileGS:
                unit(missile, bG, bS)

        score(count1)

        if success:
            unit(explore, rockG, rockS)

            rock = pygame.image.load('rock.png')
            rocksize = rock.get_rect().size
            rockgaro = rocksize[0]
            rocksero = rocksize[1]
            rockG = random.randrange(0, garo - rockgaro)
            rockS = 0
            success = False

            rockspeed += 0.05
            if rockspeed >= 10:
                rockspeed = 10

        unit(rock, rockG, rockS)

        rockS += rockspeed

        if rockS > sero:
            rock = pygame.image.load('rock.png')
            rocksize = rock.get_rect().size
            rockgaro = rocksize[0]
            rocksero = rocksize[1]
            rockG = random.randrange(0, garo - rockgaro)
            rockS = 0
            passed += 1

        if passed == 5:
            gameover()

        passed1(passed)

        x += fighterjetX
        if x < 0:
            x = 0
        elif x > garo - fighterjetgaro:
            x = garo - fighterjetgaro

        y += fighterjetY
        if y < 0:
            y = 0
        elif y > sero - fighterjetsero:
            y = sero - fighterjetsero

        if y < rockS + rocksero:
            if (rockG > x and rockG < x +fighterjetgaro) or (rockG + rockgaro > x and rockG + rockgaro < x +fighterjetgaro):
                crash()
        if count1 > 30:
            unit(ufo, ufoX, ufoY)
        if count1 > 50:
            unit(ufo, A, B)
        if count1 > 150:
            fail('성공!')
                
        pygame.display.update()
        clock.tick(60)
        

    pygame.quit()

Game()
Game1()
```
