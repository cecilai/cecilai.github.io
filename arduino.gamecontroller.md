# Arduino Joystick Python Game – Space Dodger

### Overview

For this project, I wanted to see if I could use an Arduino joystick to control a Python game instead of using my keyboard. I already had some Python games that used keyboard controls, so I thought it would be interesting to try using a controller I built myself. Since I don't know how to code yet, I used Claude AI to help write the Arduino and Python code. My part was connecting the wires, uploading the code, installing the programs, testing everything, and figuring out what was wrong whenever something didn't work.

The game I made is called Space Dodger. You control a spaceship using the joystick, collect yellow stars, and avoid meteors. After collecting 4 stars, your shield fills up. You can then press the joystick button to activate it for a few seconds.

### Why I Chose This

I chose this project because I wanted to try something different from the usual keyboard-controlled games. I thought it would be cool to build my own controller and use it to play a game on my laptop. I also wanted to see how an Arduino could communicate with a Python program.

### New Components & Sources

- Input: Arduino joystick module
- Output: Python game displayed on my laptop
- Code: Written with help from Claude AI
- Programs: Arduino IDE, Python, pygame-ce, and pyserial

### Step 1: Wiring the Joystick

The joystick has 5 pins, and each one needed to connect to a specific pin on the Arduino. I used jumper wires to connect everything. I asked the AI which pins to use and followed its instructions.

### Step 2: Uploading the Arduino Code

After wiring everything, I copied the Arduino code into the Arduino IDE and uploaded it to the board. I opened the Serial Monitor to test it. Numbers appeared on the screen, and they changed depending on which direction I moved the joystick. This showed me that the Arduino was reading my movements correctly.

### Step 3: Getting the Game to Run

Once the joystick was working, I moved on to running the Python game. I used Mac Terminal to open it, but this ended up taking the longest because I ran into several errors before I could actually play.

### Problems I Ran Into

**1. Python couldn't find pygame.**

At first, I got an error saying "No module named pygame." I had already installed it, so I didn't understand why it wasn't working. After showing the error to the AI, I found out that I had installed it for a different version of Python. I had to use a different command to install it in the correct place.

**2. Pygame wouldn't install.**

When I tried installing pygame again, the installation failed with a really long error message. I sent the error to the AI, and it explained that my version of Python was too new for the regular pygame package. Instead, I installed pygame-ce, which worked.

**3. The game crashed when I opened it.**

After getting the program to run, another error appeared about an invalid color. The AI helped me figure out that one of the color values in the code was higher than 255, which wasn't allowed. After fixing that number, the game opened properly.

**4. The joystick wouldn't connect to the game.**

I also found out that the Arduino Serial Monitor and the Python game couldn't use the joystick at the same time. I had to close the Serial Monitor before starting the game so Python could connect to the Arduino.

### Final Build

**Joystick wiring:**

- GND → GND
- +5V → 5V
- VRx → A0
- VRy → A1
- SW → Pin 2

**Arduino Code (written with Claude AI):**

```cpp
// Arduino joystick -> Python games
// Wiring: GND -> GND, +5V -> 5V, VRx -> A0, VRy -> A1, SW -> D2

const int PIN_X = A0;
const int PIN_Y = A1;
const int PIN_SW = 2;

void setup() {
  Serial.begin(9600);
  pinMode(PIN_SW, INPUT_PULLUP);
}

void loop() {
  int x = analogRead(PIN_X);
  int y = analogRead(PIN_Y);
  int pressed = digitalRead(PIN_SW) == LOW ? 1 : 0;

  Serial.print(x);
  Serial.print(',');
  Serial.print(y);
  Serial.print(',');
  Serial.println(pressed);

  delay(20);
}
```

**Python Game Code – space_dodger.py (written with Claude AI):**

```python
import math
import random
import sys
import threading
import time
import pygame

# Arduino joystick
class Joystick:
    def __init__(self, deadzone=0.2):
        self.x = 0.0
        self.y = 0.0
        self.pressed = False
        self.connected = False
        self.deadzone = deadzone

        try:
            import serial
            import serial.tools.list_ports

            ports = list(serial.tools.list_ports.comports())
            port = None

            for p in ports:
                text = f"{p.description} {p.manufacturer or ''} {p.device}".lower()
                if any(w in text for w in ("arduino", "ch340", "usb serial", "usbmodem", "usbserial", "wch")):
                    port = p.device
                    break

            if port is None and ports:
                port = ports[0].device

            if port is None:
                print("No Arduino found - using keyboard.")
                return

            self.ser = serial.Serial(port, 9600, timeout=1)
            time.sleep(2)
            self.ser.reset_input_buffer()
            self.connected = True

            threading.Thread(target=self._read, daemon=True).start()
            print(f"Joystick connected on {port}")

        except Exception as e:
            print(f"Joystick not available ({e}) - using keyboard.")

    def _read(self):
        while True:
            try:
                line = self.ser.readline().decode(errors="ignore").strip()
                rx, ry, b = (int(v) for v in line.split(","))
            except ValueError:
                continue
            except Exception:
                self.connected = False
                return

            x = (rx - 512) / 512
            y = (ry - 512) / 512

            self.x = 0.0 if abs(x) < self.deadzone else max(-1.0, min(1.0, x))
            self.y = 0.0 if abs(y) < self.deadzone else max(-1.0, min(1.0, y))
            self.pressed = b == 1


INVERT_X = False
INVERT_Y = False

# Game setup
WIDTH, HEIGHT = 800, 600
FPS = 60

pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space Dodger")
clock = pygame.time.Clock()

font = pygame.font.SysFont("arial", 24, bold=True)
big_font = pygame.font.SysFont("arial", 64, bold=True)

stick = Joystick()

stars_bg = [
    [random.randint(0, WIDTH), random.randint(0, HEIGHT), random.uniform(1, 3)]
    for _ in range(120)
]


def read_controls():
    keys = pygame.key.get_pressed()

    dx = stick.x * (-1 if INVERT_X else 1)
    dy = stick.y * (-1 if INVERT_Y else 1)

    if keys[pygame.K_LEFT] or keys[pygame.K_a]:
        dx = -1
    if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
        dx = 1
    if keys[pygame.K_UP] or keys[pygame.K_w]:
        dy = -1
    if keys[pygame.K_DOWN] or keys[pygame.K_s]:
        dy = 1

    button = stick.pressed or keys[pygame.K_SPACE]
    return dx, dy, button


class Player:
    def __init__(self):
        self.x = WIDTH / 2
        self.y = HEIGHT - 100
        self.speed = 7
        self.radius = 18
        self.shield_charge = 0
        self.shield_time = 0

    def update(self, dx, dy):
        self.x = max(self.radius, min(WIDTH - self.radius, self.x + dx * self.speed))
        self.y = max(self.radius, min(HEIGHT - self.radius, self.y + dy * self.speed))

        if self.shield_time > 0:
            self.shield_time -= 1

    def draw(self, surf):
        x, y = self.x, self.y

        flame = random.randint(10, 18)
        pygame.draw.polygon(
            surf, (255, 140, 0),
            [(x - 7, y + 14), (x + 7, y + 14), (x, y + 14 + flame)]
        )

        pygame.draw.polygon(
            surf, (90, 200, 255),
            [(x, y - 22), (x - 18, y + 16), (x + 18, y + 16)]
        )

        pygame.draw.circle(surf, (230, 250, 255), (int(x), int(y - 2)), 6)

        if self.shield_time > 0:
            pulse = 4 * math.sin(pygame.time.get_ticks() / 80)
            pygame.draw.circle(
                surf, (120, 255, 200),
                (int(x), int(y)), int(32 + pulse), 3
            )


class Meteor:
    def __init__(self, level):
        self.radius = random.randint(14, 34)
        self.x = random.randint(self.radius, WIDTH - self.radius)
        self.y = -self.radius
        self.vy = random.uniform(2.5, 4.5) + level * 0.5
        self.vx = random.uniform(-1.5, 1.5)
        self.angle = 0
        self.spin = random.uniform(-3, 3)

        n = 9
        self.shape = [
            (
                math.cos(2 * math.pi * i / n) * self.radius * random.uniform(0.75, 1.1),
                math.sin(2 * math.pi * i / n) * self.radius * random.uniform(0.75, 1.1)
            )
            for i in range(n)
        ]

    def update(self):
        self.x += self.vx
        self.y += self.vy
        self.angle += self.spin

    def draw(self, surf):
        a = math.radians(self.angle)

        pts = [
            (
                self.x + px * math.cos(a) - py * math.sin(a),
                self.y + px * math.sin(a) + py * math.cos(a)
            )
            for px, py in self.shape
        ]

        pygame.draw.polygon(surf, (140, 110, 90), pts)
        pygame.draw.polygon(surf, (90, 70, 55), pts, 3)


class Star:
    def __init__(self):
        self.x = random.randint(20, WIDTH - 20)
        self.y = -20
        self.vy = 3
        self.radius = 12

    def update(self):
        self.y += self.vy

    def draw(self, surf):
        t = pygame.time.get_ticks() / 200
        pts = []

        for i in range(10):
            r = self.radius if i % 2 == 0 else self.radius / 2.2
            ang = t + i * math.pi / 5
            pts.append((self.x + r * math.cos(ang), self.y + r * math.sin(ang)))

        pygame.draw.polygon(surf, (255, 220, 50), pts)


class Particle:
    def __init__(self, x, y, color):
        self.x, self.y = x, y
        ang = random.uniform(0, 2 * math.pi)
        spd = random.uniform(1, 6)
        self.vx = math.cos(ang) * spd
        self.vy = math.sin(ang) * spd
        self.life = random.randint(20, 40)
        self.color = color

    def update(self):
        self.x += self.vx
        self.y += self.vy
        self.life -= 1

    def draw(self, surf):
        if self.life > 0:
            pygame.draw.circle(
                surf, self.color, (int(self.x), int(self.y)),
                max(1, self.life // 10)
            )


def hit(a, b, extra=0):
    return math.hypot(a.x - b.x, a.y - b.y) < a.radius + b.radius - extra


def draw_background():
    screen.fill((8, 8, 25))

    for s in stars_bg:
        s[1] += s[2]

        if s[1] > HEIGHT:
            s[0], s[1] = random.randint(0, WIDTH), 0

        shade = int(70 * s[2])
        pygame.draw.circle(
            screen, (shade, shade, min(255, shade + 20)),
            (int(s[0]), int(s[1])), 1
        )


def center_text(text, f, y, color=(255, 255, 255)):
    img = f.render(text, True, color)
    screen.blit(img, (WIDTH / 2 - img.get_width() / 2, y))


def wait_for_button(title, subtitle):
    released = False

    while True:
        for e in pygame.event.get():
            if e.type == pygame.QUIT or (
                e.type == pygame.KEYDOWN and e.key == pygame.K_ESCAPE
            ):
                pygame.quit()
                sys.exit()

        _, _, button = read_controls()

        if not button:
            released = True
        elif released:
            return

        draw_background()

        center_text(title, big_font, 190, (90, 200, 255))

        for i, line in enumerate(subtitle):
            center_text(line, font, 300 + i * 36)

        status = "Joystick connected" if stick.connected else "No joystick - using keyboard"

        center_text(
            status, font, HEIGHT - 50,
            (120, 255, 200) if stick.connected else (255, 150, 150)
        )

        pygame.display.flip()
        clock.tick(FPS)


def play():
    player = Player()
    meteors, stars, particles = [], [], []
    score, frame = 0, 0
    button_was_down = True

    while True:
        for e in pygame.event.get():
            if e.type == pygame.QUIT or (
                e.type == pygame.KEYDOWN and e.key == pygame.K_ESCAPE
            ):
                pygame.quit()
                sys.exit()

        frame += 1
        level = score // 10

        dx, dy, button = read_controls()

        if button and not button_was_down and player.shield_charge >= 100:
            player.shield_time = FPS * 3
            player.shield_charge = 0

        button_was_down = button
        player.update(dx, dy)

        if random.random() < 0.025 + level * 0.006:
            meteors.append(Meteor(level))

        if random.random() < 0.012:
            stars.append(Star())

        for m in meteors:
            m.update()

        for s in stars:
            s.update()

        for p in particles:
            p.update()

        # Collect stars
        for s in stars[:]:
            if hit(player, s):
                stars.remove(s)
                score += 1
                player.shield_charge = min(100, player.shield_charge + 25)

                particles += [
                    Particle(s.x, s.y, (255, 220, 50))
                    for _ in range(12)
                ]

        # Meteor collisions
        for m in meteors[:]:
            if hit(player, m, extra=6):
                if player.shield_time > 0:
                    meteors.remove(m)
                    score += 1

                    particles += [
                        Particle(m.x, m.y, (160, 130, 100))
                        for _ in range(20)
                    ]
                else:
                    return score

        meteors = [m for m in meteors if m.y < HEIGHT + 50]
        stars = [s for s in stars if s.y < HEIGHT + 30]
        particles = [p for p in particles if p.life > 0]

        draw_background()

        for s in stars:
            s.draw(screen)

        for m in meteors:
            m.draw(screen)

        for p in particles:
            p.draw(screen)

        player.draw(screen)

        screen.blit(
            font.render(f"Score: {score}", True, (255, 255, 255)),
            (15, 12)
        )

        screen.blit(
            font.render(f"Level: {level + 1}", True, (255, 255, 255)),
            (15, 42)
        )

        pygame.draw.rect(
            screen, (60, 60, 80),
            (WIDTH - 215, 15, 200, 20), border_radius=6
        )

        pygame.draw.rect(
            screen, (120, 255, 200),
            (WIDTH - 215, 15, 2 * player.shield_charge, 20),
            border_radius=6
        )

        label = (
            "SHIELD READY - press button!"
            if player.shield_charge >= 100 else "Shield"
        )

        img = font.render(label, True, (120, 255, 200))
        screen.blit(img, (WIDTH - 15 - img.get_width(), 40))

        pygame.display.flip()
        clock.tick(FPS)


def main():
    high = 0

    wait_for_button("SPACE DODGER", [
        "Move with the joystick, collect stars, dodge meteors",
        "4 stars = full shield, push the stick down to use it",
        "Press the joystick button to start",
    ])

    while True:
        score = play()
        high = max(high, score)

        wait_for_button("GAME OVER", [
            f"Score: {score} Best: {high}",
            "Press the joystick button to play again",
            "Esc to quit",
        ])


if __name__ == "__main__":
    main()
```

### Technical Tidbit: How the Joystick Works

One thing I learned is that the joystick doesn't directly send directions like "left" or "right." Instead, it sends numbers between 0 and 1023. When the joystick is in the middle, the number is usually around 512. Moving it in different directions changes those numbers. The Arduino sends the readings to my laptop, and the Python game uses them to decide where the spaceship should move. The joystick also has a separate button that I used for the shield.

### Use-Case Reflection

**Problem & who it could help:**

I thought about how not everyone finds a keyboard easy to use when playing games. A separate controller could give people another way to control a game, especially people who have difficulty using a regular keyboard.

**What I would change:**

If I continued working on this, I would try to make the controller wireless and build a case for it. Right now, the wires are exposed, so they can easily get disconnected or move around. A proper case would make it more comfortable and easier to use.

**Skill I relied on most:**

I would say troubleshooting was the biggest skill I used. Even though the AI wrote most of the code, I still had to figure out what was going wrong whenever something didn't work. I spent a lot of time copying error messages, explaining what happened, trying the suggested fixes, and testing everything again. It took a lot of attempts, but I eventually got the joystick and game working together.
