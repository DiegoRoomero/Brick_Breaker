# Brick_Breaker

import math
import pygame


B_ancho = 200
B_alto = 30
 
class Bloque(pygame.sprite.Sprite):
 
    def __init__(self, color, x, y):
         
       
        super().__init__()
         
       
        self.image = pygame.Surface([B_ancho, B_alto])
         

        self.image.fill(color)
         
        self.rect = self.image.get_rect()
         

        self.rect.x = x
        self.rect.y = y


class Tabla(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.largo = 75
        self.alto = 15
        self.image = pygame.Surface([self.largo, self.alto])
        self.image.fill((255,255,255))

        self.rect = self.image.get_rect()
        self.alto_pantalla = pygame.display.get_surface().get_height()
        self.largo_pantalla = pygame.display.get_surface().get_width()

        self.rect.y = self.alto_pantalla-self.alto

    def update(self):
        pos = pygame.mouse.get_pos()
        self.rect.x = pos[0]
        if self.rect.x > self.largo_pantalla - self.largo:
            self.rect.x = self.largo_pantalla - self.largo


 
class Bola(pygame.sprite.Sprite):
    velocidad = 10.0
     

    x = 400.0
    y = 200.0
     

    angulo = 200
 
    largo = 10
    alto = 10
     

    def __init__(self):

        super().__init__()
         
        self.image = pygame.Surface([self.largo, self.alto])
         
        self.image.fill((255,255,255))
         
        self.rect = self.image.get_rect()
         
       
        self.P_alto = pygame.display.get_surface().get_height()
        self.P_ancho = pygame.display.get_surface().get_width()
     
    def rebotar(self, diff):
         
        self.angulo = (180 - self.angulo)
        self.angulo = self.angulo - diff
     
    def update(self):
        angulo_radianes = math.radians(self.angulo)
         
       
        self.x = self.x + self.velocidad * math.sin(angulo_radianes)
        self.y = self.y - self.velocidad * math.cos(angulo_radianes)
                 
        self.rect.x = self.x
        self.rect.y = self.y
         
       
        if self.y <= 0:
            self.rebotar(0)
            self.y = 1
             
       
        if self.x <= 0:
            self.angulo = (360 - self.angulo) % 360
            self.x = 1
             

        if self.x > self.P_ancho - self.largo:
            self.angulo = (360 - self.angulo) % 360
            self.x = self.P_ancho - self.largo - 1
         

        if self.y > 600:
            return True
        else:
            return False
 


pygame.init()
 
pantalla = pygame.display.set_mode([800, 600])

pygame.mouse.set_visible(0)
 
pygame.display.set_caption('Brick Breaker')
 

fondo_pantalla = pygame.Surface(pantalla.get_size())
 
bloques = pygame.sprite.Group()
bolas = pygame.sprite.Group()
todos_los_sprites = pygame.sprite.Group()
 

tabla = Tabla()
todos_los_sprites.add(tabla)
 
bola = Bola()
todos_los_sprites.add(bola)
bolas.add(bola)
 

top = 40
 
 
 
for fila in range(5):
    for columna in range(0, 4):
        bloque = Bloque((255,255,255), columna * (B_ancho + 2) + 1, top)
        bloques.add(bloque)
        todos_los_sprites.add(bloque)

    top = top + B_alto + 2
 
game_over = False


while not game_over:
 
 
    pantalla.fill((49, 1, 128))
     

    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            game_over = True
     
    if not game_over:

        tabla.update()
        bola.update()    

    if pygame.sprite.spritecollide(tabla, bolas, False):
       
        diff = (tabla.rect.x + tabla.largo/2) - (bola.rect.x+bola.largo/2)
         
        bola.rect.y = pantalla.get_height() - tabla.alto - bola.alto - 1
        bola.rebotar(diff)
     

    bloquesmuertos = pygame.sprite.spritecollide(bola, bloques, True)
     

    if len(bloquesmuertos) > 0:
        bola.rebotar(0)

    if len(bloques) == 0:
        game_over = True
     

    todos_los_sprites.draw(pantalla)
 

    pygame.display.flip()
 
pygame.quit()
