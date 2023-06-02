# Итоговый проект игра "Арканоид
import pygame
import random

# Инициализация Pygame
pygame.init()

# Настройки окна
sc_width, sc_height = 600, 400
sc = pygame.display.set_mode((sc_width, sc_height))
pygame.display.set_caption('Arkanoid')

# Цвета
white = pygame.Color('white')
black = pygame.Color('black')
red = pygame.Color('red')

# Шрифты
font_name = pygame.font.match_font('arial')
text = pygame.font.Font(font_name, 36)

# Функция для вывода текста на экран
def draw_text(text, font, color, surface, x, y):
    text_obj = font.render(text, True, color)
    text_rect = text_obj.get_rect()
    text_rect.center = (x, y)
    surface.blit(text_obj, text_rect)

# Выбор уровня сложности
level = 2

# Начало игры
pusk, play, gameover, win = True, False, False, False

def Collision(kx, ky, ball, rect):
    if kx > 0:
        dx = ball.right - rect.left
    else:
        dx = rect.right - ball.left
    if ky > 0:
        dy = ball.bottom - rect.top
    else:
        dy = rect.bottom - ball.top

    if (dx - dy) < 5:
        kx, ky == -kx, -ky
    if dx > dy:
        ky *= -1
    if dy > dx:
        kx *= -1

    return kx, ky

width, height = 600, 650
sc = pygame.display.set_mode((width, height))
clock = pygame.time.Clock()

text = pygame.font.SysFont('Century Gothic', 40, bold=False)

# Измененное расположение блоков
block = [pygame.Rect(60 + 80 * i, 60 + 40 * j, 80, 40) for i in range(6) for j in range(5)]
ColorBlock = [pygame.Color(random.randint(50, 200), random.randint(50, 200), random.randint(50, 200)) for _ in range(len(block))]
score, kolblock = 0, len(block)

player = pygame.Rect(250, 570, 100, 10)

R = 12
ball = pygame.Rect(290, 550, R, R)
kx, ky, life = 1, -1, 3

pusk, play, gameover, win = True, False, False, False

def main_menu():
    menu = True
    while menu:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()

        sc.fill(white)
        draw_text('Arkanoid', text, black, sc, width/2, height/4)
        mouse = pygame.mouse.get_pos()
        click = pygame.mouse.get_pressed()
        if width/2 - 50 <= mouse[0] <= width/2 + 50 and height/2 - 50 <= mouse[1] <= height/2:
            pygame.draw.rect(sc, red, (width/2 - 50, height/2 - 50, 100, 50))
            if click[0] == 1:
                menu = False
        else:
            pygame.draw.rect(sc, black, (width/2 - 50, height/2 - 50, 100, 50))
        draw_text('Start', text, white, sc, width/2, height/2 - 25)

        if width/2 - 50 <= mouse[0] <= width/2 + 50 and height/2 + 50 <= mouse[1] <= height/2 + 100:
            pygame.draw.rect(sc, red, (width/2 - 50, height/2 + 50, 100, 50))
            if click[0] == 1:
                pygame.quit()
                quit()
        else:
            pygame.draw.rect(sc, black, (width/2 - 50, height/2 + 50, 100, 50))
        draw_text('Exit', text, white, sc, width/2, height/2 + 75)

        pygame.display.update()
        clock.tick(60)

main_menu()

# Основной игровой цикл
while True:
    sc.fill(pygame.Color('white'))

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            exit()
        elif event.type == pygame.MOUSEBUTTONDOWN and gameover == False and win == False:
            pusk, play = False, True

    if gameover == False and win == False:
        key = pygame.key.get_pressed()
        if key[pygame.K_d] and player.right < width:
            player.x += 10

            if pusk == True:
                ball.x += 10
        if key[pygame.K_a] and player.left > 0:
            player.x -= 10

            if pusk == True:
                ball.x -= 10

    if play == True:
        ball.x += 4 * kx
        ball.y += 4 * ky

        if ball.x < R or ball.x > width - R:
            kx *= -1
        if ball.y - 50 < R:
            ky *= -1

        if ball.colliderect(player) and ky > 0:
            kx, ky = Collision(kx, ky, ball, player)

        destroy = ball.collidelist(block)
        if destroy != -1:
            destrect = block.pop(destroy)
            
            kx, ky = Collision(kx, ky, ball, destrect)
            score += 1

        if len(block) == 0:
            win = True

        if ball.y > height:
            life -= 1
            if life > 0:
                pusk, play = True, False
                ball.x, ball.y = 290, 550
            else:
                gameover = True

    pygame.draw.rect(sc, black, player)
    pygame.draw.ellipse(sc, black, ball)

    for i, rect in enumerate(block):
        pygame.draw.rect(sc, ColorBlock[i], rect)

    draw_text('Score: ' + str(score), text, black, sc, 80, 15)
    draw_text('Life: ' + str(life), text, black, sc, width - 60, 15)

    if gameover:
        draw_text('Game Over', text, black, sc, width/2, height/2)
        draw_text('Press Space to Continue', text, black, sc, width/2, height/2 + 50)
        key = pygame.key.get_pressed()
        if key[pygame.K_SPACE]:
            gameover = False
            main_menu()

    if win:
        draw_text('You Win!', text, black, sc, width/2, height/2)
        draw_text('Press Space to Continue', text, black, sc, width/2, height/2 + 50)
        key = pygame.key.get_pressed()
        if key[pygame.K_SPACE]:
            win = False
            main_menu()

    pygame.display.update()
    clock.tick(60)

