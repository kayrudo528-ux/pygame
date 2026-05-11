import random
import time


class Enemy:
    def __init__(self, name, hp):
        self.name = name
        self.hp = hp


class Player:
    def __init__(self, name):
        self.name = name
        self.hp = 100
        self.black_flash_bonus = 0
        self.black_flash_streak = 0
        self.soul_timer = time.time()

        self.clan = self.roll_clan()
        self.apply_clan()

    def roll_clan(self):
        return random.choice(["Gojo", "Zenin", "Kamo", "Inumaki", "Okkotsu", "Geto", "Hakari", "Higuruma", "Ryomen"])

    def apply_clan(self):
        self.damage_buff = 1.0
        self.ce = 100

        if self.clan == "Ryomen":
            if random.random() < 0.1:
                self.form = "Heian"
                self.damage_buff = 1.06
                self.ce *= 1.25
            else:
                self.form = "15F"

    def black_flash(self):
        chance = 0.05 + (self.black_flash_bonus / 100)

        if random.random() < chance:
            self.black_flash_streak += 1
            self.black_flash_bonus = min(100, self.black_flash_bonus + 10)
            print("💥 BLACK FLASH!")
            return 1.5

        self.black_flash_streak = 0
        self.black_flash_bonus = 0
        return 1.0

    def melee_attack(self, enemy):
        dmg = random.randint(10, 20)
        dmg *= self.black_flash()
        dmg *= self.damage_buff

        enemy.hp -= dmg
        print(f"{self.name} deals {dmg:.1f} damage to {enemy.name}")

    def soul_slash(self, enemy):
        if time.time() - self.soul_timer >= 20:
            self.soul_timer = time.time()
            enemy.hp -= 25
            print("🗡 Soul Slash hits!")


def fight(player, enemy):
    print(f"{player.name} vs {enemy.name} started!")

    while player.hp > 0 and enemy.hp > 0:

        player.melee_attack(enemy)
        player.soul_slash(enemy)

        if enemy.hp <= 0:
            print("Enemy defeated!")
            break

        dmg = random.randint(5, 15)
        player.hp -= dmg
        print(f"Enemy hits back for {dmg}")

        time.sleep(1)

    if player.hp <= 0:
        print("You lost!")


player = Player("Yuji")
enemy = Enemy("Special Grade Curse", 120)

fight(player, enemy)

