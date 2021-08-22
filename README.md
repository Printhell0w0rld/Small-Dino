# Small-Dino
There is still a small bug in this game, so don't change any value in case you can't play the game.
Have Fun!!
```Python
import pygame,sys
import time
import random

FPS=60
WIDTH=500
HEIGHT=500
blockw=20
blockh=40
compare=0
Time=0
score=0
maxscore=0
check=False
emergency=0

pygame.init()
pygame.display.set_caption('Small Game')# name of the window
screen=pygame.display.set_mode([WIDTH,HEIGHT])
clock=pygame.time.Clock()

class Dino(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image=pygame.Surface((blockw,blockh))
        self.image.fill([255,255,255])
        self.rect=self.image.get_rect()
        self.rect.x=140;
        self.rect.y=HEIGHT//2+40-blockh
    
    def update(self):
        global Time,compare,emergency
        key_pressed=pygame.key.get_pressed()
        if key_pressed[pygame.K_SPACE]:
            compare=1
        if compare and Time<23:
            if(Time<10):
                self.rect.y=self.rect.y-8
                Time+=1
            elif(Time>=10 and Time<13):
                Time+=1
            else:
                self.rect.y=self.rect.y+8
                Time+=1
        if Time==23:
            Time=0
            compare=0
        if key_pressed[pygame.K_DOWN]:
            self.image=pygame.Surface((blockw,blockh-20))
            if emergency==0:
                self.rect.y=self.rect.y+20; 
                emergency=1
            self.image.fill([255,255,255])
            
        elif emergency==1:
            self.image=pygame.Surface((blockw,blockh))
            self.rect.y=self.rect.y-20;
            self.image.fill([255,255,255])
            emergency=0
        



class Barrier(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        #Here's a small bug haven't debugged yet
        stone_height=20
        self.image=pygame.Surface((10,stone_height))
        self.rect=self.image.get_rect()
        self.rect.x=500;
        self.rect.y=HEIGHT//2+40-stone_height; 
        self.image.fill([255,255,255])
    def update(self): 
        global score
        self.rect.x-=5
        if self.rect.x%20==0:
            score+=1
        if self.rect.x<0:
            self.rect.x=500
            decision=random.randint(1,4)
            if(decision==1):
                self.image=pygame.Surface((10,10))
                self.rect.y=HEIGHT//2-5
                self.image.fill([255,255,255])
            else:
                stone_h=random.randint(20,50)
                self.image=pygame.Surface((10,stone_h))
                self.rect.y=HEIGHT//2+40-stone_h; 
                self.image.fill([255,255,255])
                

font_name=pygame.font.match_font('arial')
def draw_text(surf,text,size,x,y):
    font=pygame.font.Font(font_name,size)
    text_surface=font.render(text,True,[255,255,255])
    text_rect=text_surface.get_rect()
    text_rect.centerx=x
    text_rect.top=y
    surf.blit(text_surface,text_rect)

def draw_init():
    global maxscore
    screen.fill((0,0,0))
    draw_text(screen,'Click any bottom to start',50,WIDTH//2,HEIGHT//3+20)
    draw_text(screen,'Max  Score:',20,WIDTH//2-40,HEIGHT//3+80)
    draw_text(screen,str(maxscore),20,WIDTH//2+40,HEIGHT//3+80)
    pygame.display.update()
    waiting=True
    while waiting:
        clock.tick(FPS)
        for event in pygame.event.get():
            if event.type==pygame.QUIT:
                sys.exit()
            elif event.type==pygame.KEYDOWN:
                waiting=False


all_sprites=pygame.sprite.Group()
bar=pygame.sprite.Group()
dino=Dino()
barrier=Barrier()
all_sprites.add(dino)
bar.add(barrier)
bar.update()
all_sprites.update()


show_init=True
while True:
    if show_init:
        draw_init()
        show_init=False
    clock.tick(FPS)
    for event in pygame.event.get():
        if event.type==pygame.QUIT:
            sys.exit()
    #score+=1
    hits=pygame.sprite.spritecollide(dino,bar,False)
    if hits:
        show_init=True
        all_sprites=pygame.sprite.Group()
        dino=Dino()
        all_sprites.add(dino)
        bar=pygame.sprite.Group()
        barrier=Barrier()
        bar.add(barrier)
        Time=0
        maxscore=max(maxscore,score)
        score=0
        check=0
        emergency=0
        

    screen.fill((0,0,0))
    pygame.draw.rect(screen,[0,0,0],[100,100,100,100],0)
    pygame.draw.line(screen,[255,255,255],[0,HEIGHT//2+40],[500,HEIGHT//2+40],1)
    all_sprites.update()
    bar.update()
    all_sprites.draw(screen) 
    bar.draw(screen) 
    draw_text(screen,str(score),18,WIDTH//2,10)
    pygame.display.update()
```
