Code Autopsy: Deep Swim

Prompt I gave the AI: “Single HTML page using canvas, all CSS/JS inline. Tap to swim upward and release to sink. Dodge jellyfish, collect bubbles, and survive as long as possible.”

What the game does: You control a fish. Hold to swim up, let go to sink. Dodge the jellyfish and grab bubbles for points.

1. The canvas
javascript
const W = 400, H = 640;

The canvas is the drawing board for the game. This line sets its size to 400 wide and 640 tall. If I changed these numbers, the game area would get bigger or smaller.

2. State variables
javascript
let state = "ready";
let holding = false;

These are things the game remembers. state says if the game is ready, playing, or over. holding says if I’m pressing down right now. Others include the fish’s position, the jellyfish, and the score.

3. The game loop
javascript
requestAnimationFrame(loop);

This line makes the loop function run again and again, about 60 times a second. Each time, the game moves everything, checks for hits, and redraws the screen, like flipping through a flipbook.

4. Input handling
javascript
window.addEventListener("pointerdown", () => { holding = true; });
window.addEventListener("pointerup", () => { holding = false; });

When I press down, holding becomes true. When I let go, it becomes false. The game checks holding to decide if the fish goes up or down.

5. Collision / game over
javascript
if (bellHit || tentacleHit) endGame();

The game measures the distance between the fish and each jellyfish. If they overlap, endGame() runs and the game is over.

6. Scoring
javascript
function getScore() { return Math.floor(time) + bubbleCount * 5; }

The score is the seconds I survived plus 5 points for each bubble.

My deliberate change

Changed: fish.vy += 0.28 * dt to fish.vy += 0.10 * dt
Predicted: The fish would sink more slowly.
Happened: The fish floated down slowly and the game felt easier.
Learned: One small number controls how the game feels.

What I learned

A game is a lot of small parts working together. It remembers a few things, loops about 60 times a second, and each time it moves things, checks for hits, and redraws. I was surprised that pressing the screen doesn’t move the fish directly. It just flips the holding switch, and the rest of the code reacts to it.













































































