# 2D — Platformer Demo

A small platformer using the engine's built-in startPlatformer helper. It creates a player, a few platforms, and simple enemy stub. This demo uses the engine's platformer helper; tune physics or callback logic as needed.

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8"/>
  <title>gameengine — 2D Platformer Demo</title>
  <style>html,body{height:100%;margin:0;background:#0e1116}canvas{display:block;margin:0 auto}</style>
</head>
<body>
  <script src="path/to/gameengine.js"></script>
  <script>
    const game = new Game();
    game.initCanvas(900, 540);

    // Player sprite
    const player = new Sprite(40, 56, { color: "#ffd166" }).setPosition(60, 420);

    // Platforms (simple blocks)
    const platforms = [
      new Sprite(900, 40, { color: "#1b2430" }).setPosition(450, 520), // ground
      new Sprite(160, 20, { color: "#2a6f97" }).setPosition(260, 400),
      new Sprite(120, 20, { color: "#2a6f97" }).setPosition(520, 330),
      new Sprite(160, 20, { color: "#2a6f97" }).setPosition(740, 240),
    ];

    // Enemies (stationary colored sprites for demo)
    const enemies = [
      new Sprite(40, 40, { color: "#ff6b6b" }).setPosition(420, 480),
    ];

    // Add sprites to engine so they draw correctly
    game.add(...platforms);
    game.add(player);
    game.add(...enemies);

    // Start the platformer helper — uses engine's physics and detection
    game.startPlatformer({
      player,
      platforms,
      enemies,
      spawn: { x: 60, y: 420 },
      onUpdate: (state) => {
        // state contains platformer state each frame — log or inspect in devtools
        // console.log(state);
      },
      onEnd: (won, result) => {
        alert(won ? "You won!" : "Game over");
      }
    });
  </script>
</body>
</html>
```

Notes:
- This relies on the engine's startPlatformer implementation — tweak platform positions and sizes for different levels.
- Add images by using `player.asset = "player"` and `game.loadAssets(...)` before starting if you want sprites instead of colored boxes.
