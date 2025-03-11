# PBO-tugas2
import random

class Robot:
    def __init__(self, name, hp, attack_power, accuracy=0.8, defense=2):
        self.name = name
        self.hp = hp
        self.attack_power = attack_power
        self.accuracy = accuracy
        self.defense = defense
        self.is_defending = False

    def attack_enemy(self, target):
        if random.random() < self.accuracy:
            damage = self.attack_power
            if target.is_defending:
                damage = max(damage - target.defense, 0)
            target.take_damage(damage)
            return True, damage
        else:
            return False, 0

    def take_damage(self, damage):
        self.hp -= damage
        if self.hp < 0:
            self.hp = 0

    def defend(self):
        self.is_defending = True

    def is_alive(self):
        return self.hp > 0

class Game:
    def __init__(self, robot1, robot2):
        self.robot1 = robot1
        self.robot2 = robot2
        self.round = 1

    def start_game(self):
        while True:
            print(f"\nRound-{self.round} {'='*60}")
            self.display_status()

            action1 = self.get_action_input(self.robot1)
            if action1 == '3':
                print(f"\n{self.robot2.name} menang!")
                return

            action2 = self.get_action_input(self.robot2)
            if action2 == '3':
                print(f"\n{self.robot1.name} menang!")
                return

            self.robot1.is_defending = False
            self.robot2.is_defending = False

            if action1 == '2':
                self.robot1.defend()
            if action2 == '2':
                self.robot2.defend()

            attack_occurred = False

            if action1 == '1':
                hit, damage = self.robot1.attack_enemy(self.robot2)
                attack_occurred = True
                if hit:
                    print(f"\n------------ {self.robot1.name} menyerang {self.robot2.name} dengan {damage} damage ----------------")
                    if not self.robot2.is_alive():
                        print(f"{self.robot1.name} menang!")
                        return
                else:
                    print(f"\n------------ {self.robot1.name} gagal menyerang ----------------")

            if not self.robot2.is_alive():
                return

            if action2 == '1':
                hit, damage = self.robot2.attack_enemy(self.robot1)
                attack_occurred = True
                if hit:
                    print(f"\n------------ {self.robot2.name} menyerang {self.robot1.name} dengan {damage} damage ----------------")
                    if not self.robot1.is_alive():
                        print(f"{self.robot2.name} menang!")
                        return
                else:
                    print(f"\n------------ {self.robot2.name} gagal menyerang ----------------")

            if not self.robot1.is_alive():
                return

            if not attack_occurred:
                print("\n------------ Tidak ada serangan yang terjadi ----------------")

            self.round += 1

    def display_status(self):
        print(f"{self.robot1.name} [{self.robot1.hp}|{self.robot1.attack_power}]")
        print(f"{self.robot2.name} [{self.robot2.hp}|{self.robot2.attack_power}]\n")

    def get_action_input(self, robot):
        while True:
            print("1. Attack     2. Defense     3. Giveup")
            choice = input(f"{robot.name}, pilih aksi: ").strip()
            if choice in ('1', '2', '3'):
                return choice
            print("Pilihan tidak valid. Silakan pilih 1, 2, atau 3.")

# Contoh penggunaan
if __name__ == "__main__":
    robot1 = Robot("Atreus", 500, 10, accuracy=0.9, defense=2)
    robot2 = Robot("Daedalus", 750, 8, accuracy=0.8, defense=3)
    game = Game(robot1, robot2)
    game.start_game()
