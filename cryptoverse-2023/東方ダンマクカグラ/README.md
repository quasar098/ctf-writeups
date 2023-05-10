# 東方ダンマクカグラ

## problem

see attachment [here](https://drive.google.com/file/d/1iZt4OBfWMIkuucyY3QzwHyYmPbf7IxCs/view)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/a2d6cf44-657f-4a6a-98cc-bd40eda881ea)

Translates to:

You control Reimu Hakurei, a popular character from the Touhou Project, and take on the challenge of a danmaku battle. 
This game is a new style of game where you dodge enemy bullets and attack in rhythm. 
A flag will appear when cleared. Finding the flag requires reverse engineering the game. I wish you the best!

## solution

Unfortunately, I could not solve this during the challenge. I realized shortly after the CTF the solution.

I was so excited to see this challenge, because I have used pygame and PyInstaller for many many years now.

I knew exactly that I had to use [pyinstxtractor](https://github.com/extremecoders-re/pyinstxtractor) to decompile the binary and then use uncompyle6 to decode the .pyc files.

I was able to do this in under 5 minutes, but now you may be wondering, how did I not solve the challenge?

The decoded Main.py is as follows:

```py
import os
os.environ['PYGAME_HIDE_SUPPORT_PROMPT'] = 'hide'
import pygame, math, random, time

class sizes:

    def __init__(self, width, height):
        self.Width = width
        self.Height = height


currentCount = 0
currentAnim = 1
animLimit = False
playerX = 400
playerY = 450
lastBullet = 0
bulletLimit = 5000
totalBullets = 0
toX = 0
toY = 0
bullets = []
bulletsDirection = [
 1]
bulletsY = []
bulletsX = []

def main():
    global bullets
    global bulletsX
    global bulletsY
    global currentAnim
    global currentCount
    global lastBullet
    global playerX
    global playerY
    global toX
    global toY
    global totalBullets
    pygame.init()
    pygame.mixer.init()
    pygame.mixer.music.load('Song.mp3')
    pygame.mixer.music.set_volume(0.2)
    pygame.mixer.music.play()
    screenSizes = sizes(800, 600)
    screen = pygame.display.set_mode((screenSizes.Width, screenSizes.Height))
    startBullet = pygame.image.load('Bullet.png')
    startBullet = screen.blit(startBullet, (450, 300))
    bullets.append(startBullet)
    bulletsX.append(450)
    bulletsY.append(300)
    pygame.display.set_caption('Touhou Danmaku Kagura')
    pygame.display.set_icon(pygame.image.load('Icon.png'))
    background = pygame.image.load('Background.jpg')
    background = pygame.transform.scale(background, (screenSizes.Width, screenSizes.Height))

    def getPlayerDirection():
        global animLimit
        if toX == 0:
            animLimit = bool(False)
            return 'Idle'
        if toX >= 1:
            animLimit = bool(True)
            return 'Right'
        if toX <= -1:
            animLimit = bool(True)
            return 'Left'

    def getAnimation():
        global currentAnim
        global currentCount
        if currentCount >= 150:
            if currentAnim <= 7:
                currentCount = 0
                currentAnim += 1
            else:
                if animLimit == False:
                    currentCount = 0
                    currentAnim = 1

    def loadBullet(x, y):
        newBullet = pygame.image.load('Bullet.png')
        newBullet = screen.blit(newBullet, (x, y))

    def bullet(x, y):
        newBullet = pygame.image.load('Bullet.png')
        newBullet = screen.blit(newBullet, (x, y))
        bulletsDirection.append(random.randint(1, 2))
        bullets.append(newBullet)
        bulletsX.append(x)
        bulletsY.append(y)

    def editDirection():
        global playerX
        global playerY
        if toY <= -1:
            if playerY <= 0:
                pass
            else:
                playerY -= 1
        elif toY >= 1:
            if playerY >= screenSizes.Height - 60:
                pass
            else:
                playerY += 1
        if toX <= -1:
            if playerX <= 0:
                pass
            else:
                playerX -= 1
        elif toX >= 1:
            if playerX >= screenSizes.Width - 25:
                pass
            else:
                playerX += 1

    def isCollision(bulletX, bulletY):
        distance = math.sqrt(math.pow(playerX - bulletX, 2) + math.pow(playerY - bulletY, 2))
        if distance < 12:
            return True
        return False

    def makePlayer(posX, posY):
        getAnimation()
        editDirection()
        getDir = getPlayerDirection()
        screen.blit(pygame.image.load(f"Reimu{getDir}{currentAnim}.png"), (posX, posY))

    screen.fill((24, 24, 24))
    pygame.display.update()
    isGameRunning = True
    start = time.time()
    while isGameRunning and time.time() - start <= 1800:
        screen.blit(background, (0, 0))
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                isGameRunning = False
                break
            else:
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_LEFT:
                        toX = -1
                        currentAnim = 1
                    else:
                        if event.key == pygame.K_RIGHT:
                            toX = 1
                            currentAnim = 1
                        if event.type == pygame.KEYDOWN:
                            if event.key == pygame.K_DOWN:
                                toY = 1
                            else:
                                if event.key == pygame.K_UP:
                                    toY = -1
                    if event.type == pygame.KEYUP:
                        if event.key == pygame.K_LEFT:
                            if toX <= -1:
                                toX = 0
                        if event.key == pygame.K_RIGHT:
                            if toX >= 1:
                                toX = 0
                            elif event.key == pygame.K_UP:
                                if toY <= -1:
                                    toY = 0
                            elif event.key == pygame.K_DOWN:
                                if toY >= 1:
                                    toY = 0

        currentCount += 1
        lastBullet += 1
        for x in range(0, len(bullets)):
            if lastBullet >= 5:
                lastBullet = 0
                if totalBullets <= bulletLimit:
                    totalBullets += 1
                    bullet(random.randint(0, 800), 0)
            try:
                if bulletsDirection[x] == 1:
                    bulletsX[x] += 0.5
                else:
                    if bulletsDirection[x] == 2:
                        bulletsX[x] -= 0.5
                    bulletsY[x] += 1.5
                    loadBullet(bulletsX[x], bulletsY[x])
                    if bulletsY[x] >= 600:
                        totalBullets -= 1
                        bullets.pop(x)
                        bulletsX.pop(x)
                        bulletsY.pop(x)
            except:
                pass

            try:
                if isCollision(bulletsX[x], bulletsY[x]) == True:
                    totalBullets -= 1
                    bullets.pop(x)
                    bulletsX.pop(x)
                    bulletsY.pop(x)
            except:
                pass

        makePlayer(playerX, playerY)
        pygame.display.update()

    if isGameRunning:
        dx = 50
        dy = 300
        totalBullets += 1
        bullet(dx, dy)
        for _ in range(42):
            bulletsY[-1] += 1
            loadBullet(bulletsX[-1], bulletsY[-1])

        dx += 62
        bullet(dx, dy)
        for _ in range(42):
            bulletsY[-1] += 1
            loadBullet(bulletsX[-1], bulletsY[-1])

        dx += 62
        bullet(dx, dy)
        for _ in range(30):
            bulletsX[-1] -= 1
            bulletsY[-1] += 1
            loadBullet(bulletsX[-1], bulletsY[-1])

        for _ in range(30):
            bulletsX[-1] += 1
            bulletsY[-1] += 1
            loadBullet(bulletsX[-1], bulletsY[-1])
            
... (etc) ...
```
The description said to "clear the game", so I removed the part where the character collides with the bullet and waited for 7 minutes for the music to go away. 
I did not get the flag, so I spent a lot of time searching in the other files.
I did not see that the `time.time() - start <= 1800`, meaning you had to wait 30 whole minutes to see a binary message encoded with bullets. 
I am too lazy to solve the flag myself, but to solve it, you parse the bullet patterns at the end of the main.py (very long)
