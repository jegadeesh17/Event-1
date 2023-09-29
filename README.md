import pygame
import random
import sys
class TrashItem(pygame.sprite.Sprite):
    def _init_(self):
        super()._init_()
        self.trash_type = random.choice(TRASH_TYPES)
        self.image = pygame.Surface((50, 50))
        self.image.fill(TRASH_COLORS[self.trash_type])
        self.rect = self.image.get_rect()
        self.rect.centerx = random.randint(100, SCREEN_WIDTH - 100)
        self.rect.centery = -50  # Start above the screen

class TrashBin(pygame.sprite.Sprite):
    def _init_(self, bin_type, x):
        super()._init_()
        self.bin_type = bin_type
        self.image = pygame.Surface((100, 100))
        self.image.fill(BIN_COLORS[self.bin_type])
        self.rect = self.image.get_rect()
        self.rect.centerx = x
        self.rect.centery = SCREEN_HEIGHT - 50

class TrashSorterGame:
    def _init_(self):
        pygame.init()
        self.screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
        pygame.display.set_caption("Trash Sorter - The Eco Challenge Game")
        self.clock = pygame.time.Clock()
        self.trash_items = pygame.sprite.Group()
        self.trash_bins = pygame.sprite.Group([TrashBin("Recyclable", 200),
                                               TrashBin("Compost", 400),
                                               TrashBin("Landfill", 600)])
        self.score = 0
        self.font = pygame.font.Font(None, 36)
        self.time_left = 60

    def generate_trash(self):
        trash_item = TrashItem()
        self.trash_items.add(trash_item)

    def update(self):
        self.trash_items.update()
        for trash_item in self.trash_items:
            if trash_item.rect.colliderect(self.trash_bins):
                if trash_item.trash_type == self.trash_bins.sprites()[0].bin_type:
                    self.score += 1
                else:
                    self.score -= 1
                self.trash_items.remove(trash_item)
        if self.time_left > 0:
            self.time_left -= 1
        else:
            self.game_over()

    def game_over(self):
        while True:
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    pygame.quit()
                    sys.exit()
            self.screen.fill(WHITE)
            text = self.font.render(f"Game Over - Score: {self.score}", True, (0, 0, 0))
            text_rect = text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2))
            self.screen.blit(text, text_rect)
            pygame.display.flip()
            self.clock.tick(60)

    def play(self):
        running = True
        while running:
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    running = False
            self.screen.fill(WHITE)
            self.generate_trash()
            self.update()
            self.trash_items.draw(self.screen)
            self.trash_bins.draw(self.screen)
            score_text = self.font.render(f"Score: {self.score}", True, (0, 0, 0))
            time_text = self.font.render(f"Time Left: {self.time_left}", True, (0, 0, 0))
            self.screen.blit(score_text, (20, 20))
            self.screen.blit(time_text, (SCREEN_WIDTH - 200, 20))
            pygame.display.flip()
            self.clock.tick(60)

if _name_ == "_main_":
    game = TrashSorterGame()
    game.play()
