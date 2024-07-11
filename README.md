import pygame
import random
import time

pygame.init()

music=pygame.mixer.music.load("Match Game Theme.mp3")
pygame.mixer.music.play(-1)

screen_width=800
screen_height=600
screen=pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Kenzy's Matching Game")
font=pygame.font.Font(None, 74)

white=(255,255,255)
black=(0,0,0)
yellow=(255,255,0)
pink=(255,51,255)

card_size=100
margin=20

def createthecardpositions():
    positions=[]
    for i in range(4):
        for j in range(4):
            x=margin+j*(card_size+margin)
            y=margin+i*(card_size+margin)
            positions.append((x,y))
    return positions

positions=createthecardpositions()

def generatingpairs():
    symbols=list(range(8))*2
    random.shuffle(symbols)
    return symbols
pairs=generatingpairs()

class Card:
    def __init__(self, symbols, position):
        self.symbols=symbols
        self.position=position
        self.rect=pygame.Rect(position[0], position[1],card_size,card_size)
        self.revealed=False
        self.matched=False
    def draw(self, screen):
        if self.revealed or self.matched:
            pygame.draw.rect(screen, white, self.rect)
            text=font.render(str(self.symbols), True, black)
            screen.blit(text, (self.position[0] + 35, self.position[1] + 25))
        else:
            pygame.draw.rect(screen, yellow, self.rect)

def resetthegame():
    global cards, firstcard, secondcard, matches, attempts, game_over
    
    pairs=generatingpairs()        
    cards=[Card(pairs[i], positions[i]) for i in range(16)]
    firstcard=None
    secondcard=None
    matches=0
    attempts=0
    game_over=False

    screen.fill(black)
    for card in cards:
        card.revealed=True
        card.draw(screen)
    screen.blit(font.render("Memorize many pairs", True, white),(10,500))

    pygame.event.pump()
    pygame.display.flip()
    time.sleep(20)
    for card in cards:
        card.revealed=False


resetthegame()

running=True
clock=pygame.time.Clock()




while running:
    screen.fill(black)
    for event in pygame.event.get():
        if event.type==pygame.QUIT:
            running=False
        elif event.type==pygame.MOUSEBUTTONDOWN:
            if game_over:
                if playagain_button.collidepoint(event.pos):
                    resetthegame()
            else: 
                if firstcard is None or (firstcard and secondcard is None):
                    for card in cards:
                        if card.rect.collidepoint(event.pos) and not card.revealed and not card.matched:
                            card.revealed=True
                            if firstcard is None:
                                firstcard=card
                            elif secondcard is None:
                                secondcard=card
                                attempts+=1
                            
    if firstcard and secondcard:
        pygame.time.wait(500)
        if firstcard.symbols==secondcard.symbols:
            firstcard.matched=True
            secondcard.matched=True
            firstcard=None
            secondcard=None
            matches+=1
        else:
            firstcard.revealed=False
            secondcard.revealed=False
            firstcard=None
            secondcard=None

    for card in cards:
        card.draw(screen)

    text=font.render(f"Matches: {matches}  Attempts: {attempts}", True, white)
    screen.blit(text, (10, 500))

    if matches==8:
        screen.fill(black)
        win_text=font.render("Congratulations!", True, pink)
        screen.blit(win_text, (screen_width//2 - 100, screen_height//2-50))

    if attempts>=20:
        screen.fill(black)
        lose_text=font.render("You lost", True, pink)
        screen.blit(lose_text, (screen_width//2 - 100, screen_height//2-50))
        game_over=True

    if game_over:
        playagain_button=pygame.Rect(screen_width//2 - 100, screen_height//2+50,200,50)
        pygame.draw.rect(screen, yellow, playagain_button)
        newtext=font.render("Restart", True, white)
        screen.blit(newtext, (screen_width//2-65, screen_height//2+60))
        
    pygame.display.flip()
    
    clock.tick(30)

pygame.quit()
        
        
                        
        

