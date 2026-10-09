window.addEventListener("pointerdown", () => { holding = true; });
window.addEventListener("pointerup", () => { holding = false; });

The game keeps a simple yes/no note called holding. When you press your finger or mouse down, the note says “yes.” When you let go, it says “no.” That is the only control in the whole game. Everything else the fish does depends on that one note.



























if (holding) fish.vy -= 0.55 * dt; else fish.vy += 0.28 * dt;
fish.vy = Math.max(-6, Math.min(5, fish.vy));
fish.y += fish.vy * dt;

The fish has a speed that points up or down. If you are holding, the game nudges the fish’s speed upward. If you are not, it nudges the speed downward, a little more gently. The second line sets a speed limit so the fish never gets too fast, and the third line moves the fish by its current speed. Because the speed changes gradually instead of instantly, the fish drifts and floats like it is really in water.

































const difficulty = Math.min(time / 60, 1);
const speed = 2.2 + difficulty * 2.4;
...
jellyTimer -= sec;
if (jellyTimer <= 0) { spawnJelly(speed); jellyTimer = 1.5 - difficulty * 0.8 + rand(0, 0.4); }

The game has a  number that slowly grows from 0 to 1 during your first minute of playing. As that number grows, the jellyfish move faster and show up more often. A countdown timer decides when the next jellyfish appears. When it hits zero, a new jellyfish is created and the timer starts over. A small random amount is added each time, so the jellyfish don’t arrive in a predictable pattern.


































const bellHit = Math.hypot(fish.x - j.x, fish.y - j.y) < j.r * 0.9 + 9;
const tentacleHit = Math.hypot(fish.x - j.x, fish.y - (j.y + j.r * 0.9)) < j.r * 0.5 + 8;
if (bellHit || tentacleHit) endGame();

To know if the fish touched a jellyfish, the game measures the distance between them. If they are closer than the sizes of the two shapes added together, they must be overlapping, which counts as a hit. Each jellyfish has two invisible circles: one for its round top and one lower down for its tentacles. If the fish touches either one, the game ends. The circles are made slightly smaller than the drawing so that close calls feel fair instead of unfair.


































function loop(now) {
  const dt = Math.min((now - lastTime) / 16.667, 2);
  lastTime = now;
  if (state === "playing") update(dt);
  drawBackground();
  for (const b of bubbles) drawBubble(b);
  for (const j of jellies) drawJelly(j);
  drawFish();
  if (state === "playing") drawHud();
  requestAnimationFrame(loop);
}


This is the game’s heartbeat. It runs over and over, about 60 times every second, like the pages of a flipbook. Each time, the game does the same three things: (1) checks how much time has passed, (2) moves everything and checks for hits, and (3) redraws the whole scene from the background to the front. Because it redraws so quickly, the pictures look like smooth movement. Checking the time passed also keeps the game at the same speed on fast and slow devices.






