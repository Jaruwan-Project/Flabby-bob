# Flabby-bob
# Title - Pause Menu and Crash Menu
#
# Credited to
# Hein Khant Zaw | ID - 6118157 | VMS Student | Assumption University

import random
import pygame
import datetime

pygame.init()

# Width and Height of the game window
displayWidth = 500
displayHeight = 600

# Setting up environment
gameDisplay = pygame.display.set_mode((displayWidth, displayHeight))
pygame.display.set_caption("Flappy Bob")
background_image = pygame.image.load('background.png')
background_image = pygame.transform.scale(background_image, (500, 700))

# Player declaration of images
flappy1 = pygame.image.load('flappy1.png')
flappy1 = pygame.transform.scale(flappy1, (100, 100))
flappy2 = pygame.image.load('flappy2.png')
flappy2 = pygame.transform.scale(flappy2, (100, 100))

# Ghost bird
ghostBird1 = pygame.image.load('ghostBird1.png')
ghostBird1 = pygame.transform.scale(ghostBird1, (100, 100))
ghostBird2 = pygame.image.load('ghostBird2.png')
ghostBird2 = pygame.transform.scale(ghostBird2, (100, 100))

# Pipes declaration of images
obsImg = pygame.image.load('PIPE001.png')
obsImg = pygame.transform.scale(obsImg, (100, 600))
obsImg2 = pygame.image.load('PIPE002.png')
obsImg2 = pygame.transform.scale(obsImg2, (100, 600))

# Power Ups
powerBall = pygame.image.load('electricSign1.png')
powerBall = pygame.transform.scale(powerBall, (50, 50))

# Sound files
swing = pygame.mixer.Sound('sfx_wing.wav')
gotPoint = pygame.mixer.Sound('sfx_point.wav')
gotHit = pygame.mixer.Sound('sfx_hit.wav')


# For Starting screen and Pause screen
logo = pygame.image.load('flappyBOB.png')
logo = pygame.transform.scale(logo, (350, 150))
logo = pygame.transform.scale(logo, (350, 150))
pauseScreen = pygame.image.load("pauseScreen.png")
pauseScreen = pygame.transform.scale(pauseScreen, (500, 700))
crashScreen = pygame.image.load("crashScreen.png")
crashScreen = pygame.transform.scale(crashScreen, (500, 700))


# Buttons
playBtn1 = pygame.image.load("playButton1.png")
playBtn1 = pygame.transform.scale(playBtn1, (120, 70))
playBtn2 = pygame.image.load("playButton2.png")
playBtn2 = pygame.transform.scale(playBtn2, (120, 70))

quitBtn1 = pygame.image.load("quitButton1.png")
quitBtn1 = pygame.transform.scale(quitBtn1, (120, 70))
quitBtn2 = pygame.image.load("quitButton2.png")
quitBtn2 = pygame.transform.scale(quitBtn2, (120, 70))


# Colors
WHITE = (255, 255, 255, 255)
BLACK = (0, 0, 0, 0)


# Flapping effects function
def flap(playerX, playerY, state1, isEaten):
    if isEaten == False:
        displayBird1 = flappy1
        displayBird2 = flappy2
        if state1 == True:
            gameDisplay.blit(displayBird1, (playerX, playerY))
        elif state1 == False:
            gameDisplay.blit(displayBird2, (playerX, playerY))
    elif isEaten == True:
        displayBird1 = ghostBird1
        displayBird2 = ghostBird2
        if state1 == True:
            gameDisplay.blit(displayBird1, (playerX, playerY))
        elif state1 == False:
            gameDisplay.blit(displayBird2, (playerX, playerY))


# Two pipes for up and down (A pair of pipes)
def pipes1(playerX, playerY, x1, y1, speed, score, isEaten):
    gameExit = False
    x1 += speed

    # Condition for Collision between bird and pipe
    # Add if player ate powerball or not
    if isEaten == False:
        if (((x1 + 35 < playerX + 110 < x1 + 100) and (y1 - 70 < playerY < y1 + 793)) or
                ((x1 + 35 < playerX + 20 < x1 + 100) and (y1 - 70 < playerY < y1 + 793))):
            pygame.mixer.music.stop()
            pygame.mixer.Sound.play(gotHit)
            # Call crash() function when it is dead
            crash(score)
            gameExit = True

    # Collision of player and ground
    if playerY == displayHeight - 30:
        pygame.mixer.Sound.play(gotHit)
        gameExit = True

    # Condition to check if bird has passed the pipe
    if x1 + 40 < playerX < x1 + 45:
        # Add sound every time bird passes the pipe
        pygame.mixer.Sound.play(gotPoint)
        # Add score to one
        score += 1

    gameDisplay.blit(obsImg, (x1, y1))
    return x1, y1, score, gameExit


def pipes2(playerX, playerY, x10, y10, speed, score, isEaten):
    gameExit = False

    pipeHeight = 321 + y10 - 39
    x10 += speed
    if isEaten == False:
        if (((x10 + 35 < playerX + 110 < x10 + 90) and (y10 - 70 < playerY < y10 + 572)) or
                ((x10 + 35 < playerX + 20 < x10 + 90) and (y10 - 70 < playerY < y10 + 572))):
            pygame.mixer.music.stop()
            pygame.mixer.Sound.play(gotHit)
            # Call crash() function when it is dead
            crash(score)
            gameExit = True
    gameDisplay.blit(obsImg2, (x10, y10))
    return x10, y10, gameExit


def powerComing(playerX, playerY, powerBallX, powerBallY, powerSpeed, gameStarted, farFromEdge, isEaten, lastingPower):
    if isEaten == False:
        if ((powerBallX + 30 < playerX + 100 < powerBallX + 60) and (powerBallY + 30 < playerY + 90 < powerBallY + 60)
                or
                (powerBallX + 30 < playerX + 100 < powerBallX + 60) and (
                        powerBallY + 30 < playerY + 20 < powerBallY + 60)
                or
                (powerBallX + 30 < playerX + 20 < powerBallX + 60) and (
                        powerBallY + 30 < playerY + 90 < powerBallY + 60)
                or
                (powerBallX + 30 < playerX + 20 < powerBallX + 60) and (
                        powerBallY + 30 < playerY + 20 < powerBallY + 60)):
            isEaten = True
            lastingPower = datetime.datetime.utcnow() + datetime.timedelta(seconds=3)

    if gameStarted == True and isEaten == False:
        powerBallX += powerSpeed
        gameDisplay.blit(powerBall, (powerBallX, powerBallY))

    if powerBallX == -100 or isEaten == True:
        powerBallX = displayWidth + farFromEdge

    return powerBallX, powerBallY, farFromEdge, isEaten, lastingPower


def text_objects(text, font, color):
    textSurface = font.render(text, True, color)  # Define fonts color
    return textSurface, textSurface.get_rect()


# A Function to display text on the screen
# message_display function needs 5 or 6 parameters
# font can be null and valid
# If function is called with null value of font, "freesansbold.ttf" will be the default font
def message_display(text, font_size, fontX, fontY, color, font = None):
    # Default font
    if font == None:
        largeText = pygame.font.Font('freesansbold.ttf', font_size)  # Define font type and size
        TextSurf, TextRect = text_objects(text, largeText, color)  # Create text as object
        TextRect.center = (fontX, fontY)  # Define where texts will appear
        gameDisplay.blit(TextSurf, TextRect)  # Display text
        pygame.display.update()
    # Specified font
    elif font != None:
        largeText = pygame.font.Font(font, font_size)  # Define font type and size
        TextSurf, TextRect = text_objects(text, largeText, color)  # Create text as object
        TextRect.center = (fontX, fontY)  # Define where texts will appear
        gameDisplay.blit(TextSurf, TextRect)  # Display text
        pygame.display.update()


def quitgame():
    pygame.quit()
    quit()


def scoreCount(score):
    # Display score with specified font (flappy bird font)
    message_display(str(score), 100, displayWidth / 2, 70, WHITE, "slkscr.ttf")
    pygame.display.update()


# Function to display Logo in Starting screen
def logoDisplay(logoX, logoY, gameStarted):
    if gameStarted == False:
        gameDisplay.blit(logo, (logoX, logoY))



# Declaration: Sorry for repeating myself. I repeated the same function which works fine with just one function
#              This is to make easier to see when calling the function. Easier to know which function is called.
#              By just looking at function name.
#
# Function to display Play button
def playButton(x, y, action=None):
    mouse = pygame.mouse.get_pos()
    click = pygame.mouse.get_pressed()

    # Condition to check whether cursor is on the button or not
    # 'if' condition is when cursor is on the button
    # Different images on different conditions
    if x+110 > mouse[0] > x and y+70 > mouse[1] > y:
        gameDisplay.blit(playBtn2, (x, y))


        if click[0] == 1 and action != None:
            action()
    # 'else' condition is when cursor is not on the button
    else:

        gameDisplay.blit(playBtn1, (x, y))
    pygame.display.update()


# Function to display Quit button
def quitButton(x, y, action=None):
    mouse = pygame.mouse.get_pos()
    click = pygame.mouse.get_pressed()

    # Condition to check whether cursor is on the button or not
    # 'if' condition is when cursor is on the button
    # Different images on different conditions
    if x+110 > mouse[0] > x and y+70 > mouse[1] > y:
        # pygame.draw.rect(gameDisplay, ac, (x, y, w, h))
        gameDisplay.blit(quitBtn2, (x, y))


        if click[0] == 1 and action != None:
            action()

    # 'else' condition is when cursor is not on the button
    else:
        gameDisplay.blit(quitBtn1, (x, y))
    pygame.display.update()


# paused() function is to display pause menu when key 'p' is clicked
def paused(score):
    gameDisplay.blit(pauseScreen, [0,0])
    # message_display("Pause", 200, displayWidth/2, 230, WHITE, "FlappyBirdy.ttf")
    message_display(str(score), 50, displayWidth/2+100, 266, BLACK, "slkscr.ttf")
    # message_display(str(high_score), 50, (displayWidth/2)+100, 322, BLACK, "slkscr.ttf")
    while pause:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                # pause_quit = True
                pygame.quit()
                quit()
        playButton(100, 400, unpause)
        quitButton(280, 400, quitgame)
        pygame.display.update()


# unpause() function will be called when player click play button in pause menu or gameover menu (crash menu)
def unpause():
    global pause
    pause = False


# crash() function is to display current score and high score when bird is crashed
def crash(score):
    # message_display("You are Dead", 75, displayWidth/2, 200, "ShareTechMono-Regular.ttf")
    gameDisplay.blit(crashScreen, [0,0])
    global high_score   # Most important part of calling global variables
    if score > high_score:
        fileTwo = open("HScore.txt", "w")
        fileTwo.write(str(score))
        fileTwo.close()

        fileOne = open("HScore.txt", "r")
        high_score = fileOne.read()
        fileOne.close()

    # Below conditions are to align score and high score
    # Run without these conditions and you will understand
    if len(str(score)) <= 1:
        message_display(" "+str(score), 50, displayWidth / 2 + 105, 275, BLACK, "slkscr.ttf")
        if len(str(high_score)) <= 1:
            message_display(" "+str(high_score), 50, (displayWidth / 2) + 105, 322, BLACK, "slkscr.ttf")
        elif len(str(high_score)) >= 2:
            message_display(str(high_score), 50, (displayWidth / 2) + 100, 322, BLACK, "slkscr.ttf")
    elif len(str(score)) >= 1:
        message_display(" "+str(score), 50, displayWidth / 2 + 90, 275, BLACK, "slkscr.ttf")
        if len(str(high_score)) <= 1:
            message_display(" "+str(high_score), 50, (displayWidth / 2) + 105, 322, BLACK, "slkscr.ttf")
        elif len(str(high_score)) >= 2:
            message_display(str(high_score), 50, (displayWidth / 2) + 100, 322, BLACK, "slkscr.ttf")

    intro = True
    while intro:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                intro = False
                pygame.quit()
                quit()
        playButton(100, 400, mainLoop)
        quitButton(280, 400, quitgame)

        pygame.display.update()


# Main loop to run a game
def mainLoop():
    # Open a highscore file created in the same directory
    # (Saving high score in a file makes the value permanent)
    fileOne = open("HScore.txt", "r")
    global high_score
    high_score = int(fileOne.read())

    # Define pause as false and global at initial
    global pause
    pause = False


    gravity = 3

    # Speed of every pipes in a game
    speed = -4

    # Player's coordinates
    playerX = (displayWidth * 0.4)
    playerY = (displayHeight * 0.4)
    y_change = 0

    # Pipes' coordinates
    # x1, y1 are for first below pipe
    x1 = displayWidth + 250
    y1 = 400
    # x2, y2 are for second below pipe
    x2 = x1 + 300
    y2 = 400

    # x10, y10 are for first above pipe
    x10 = displayWidth + 250
    y10 = -400
    # x102, y102 are for second above pipe
    x102 = x10 + 300
    y102 = -400

    # farFromEdge variable is to set the distance of powerball from the edge
    farFromEdge = displayWidth + 500
    # isEaten is to determine whether Bob ate the powerball or not
    isEaten = False

    # These are the initial values of coordinates of powerball
    powerBallX = displayWidth + farFromEdge
    powerBallY = displayHeight * 0.5
    powerSpeed = -4
    # lastingPower is to determine how long powerball will last after eaten
    lastingPower = None

    # gameStarted is to make sure to start a game or not
    # (Otherwise pipes will be going through since the games is started running)
    gameStarted = False

    # gameExit is to make sure whether game is exited or not
    gameExit = False

    # State 1 means the initial state or freely falling state (Not flapping state)
    state1 = True

    # Score to count every time bird has passed each pipes
    score = 0

    # X and Y coordinates for logo
    # This can be done by putting the exact value directly to the parentheses
    # But to be clean, I created two variables as can be seen below
    logoX = (displayWidth * 0.16)
    logoY = 30

    while not gameExit:
        # Random the distance of powerball from the edge every loop
        farFromEdge = random.randint(1000, 2000)

        for event in pygame.event.get():

            if event.type == pygame.QUIT:
                gameExit = True

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    state1 = False
                    gameStarted = True
                    y_change = -3
                    pygame.mixer.Sound.play(swing)

                if event.key == pygame.K_p:
                    # pause becomes True if p is clicked
                    pause=True
                    paused(score)

            if event.type == pygame.KEYUP:
                if event.key == pygame.K_SPACE:
                    state1 = True
                    y_change = gravity

        playerY += y_change
        gameDisplay.blit(background_image, [0, 0])
        if playerY <= 0:
            playerY = 0
        if playerY >= displayHeight - 20:
            playerY = displayHeight - 30

        # To repeat the same pipe back to the screen
        # For below pipe
        if x1 < -80:
            x1 = displayWidth
        if x2 < -80:
            x2 = displayWidth

        if x10 < -80:
            x10 = displayWidth
        if x102 < -80:
            x102 = displayWidth
        flap(playerX, playerY, state1, isEaten)


        # To display Logo when game hasn't been started yet
        logoDisplay(logoX, logoY, gameStarted)



        powerBallX, powerBallY, farFromEdge, isEaten, lastingPower = powerComing(playerX, playerY, powerBallX,
                                                                                 powerBallY, powerSpeed,
                                                                                 gameStarted, farFromEdge,
                                                                                 isEaten, lastingPower)

        # Condition to check whether if time is up for powerball lasting
        # If time has run out, turn isEaten to False
        if isEaten == True and datetime.datetime.utcnow() > lastingPower:
            isEaten = False


        # Calling pipe function for two pipes for now (both on the ground)
        # For below pipe
        # Add score variable to pipe function as argument
        # Return the score as it has to be updated
        # (Reminder: You can just add to under function since it is no need)
        if not gameExit and gameStarted == True:
            x1, y1, score, gameExit = pipes1(playerX, playerY, x1, y1, speed, score, isEaten)
        if not gameExit and gameStarted == True:
            x2, y2, score, gameExit = pipes1(playerX, playerY, x2, y2, speed, score, isEaten)
        # For above pipe
        #
        if not gameExit and gameStarted == True:
            x10, y10, gameExit = pipes2(playerX, playerY, x10, y10, speed, score, isEaten)
        if not gameExit and gameStarted == True:
            x102, y102, gameExit = pipes2(playerX, playerY, x102, y102, speed, score, isEaten)

        # scoreCount function should be called after every functions since it should be on above all (bird, pipes, background)
        # Otherwise, pipes will be glitched in every loop ( if it is called just before pipes)
        #
        # scoreCount() function is called if game is started (Otherwise 0 will be displayed on the screen though game hasn't started yet)
        if gameStarted == True:
            scoreCount(score)
        pygame.display.update()


mainLoop()
pygame.quit()
quit()

