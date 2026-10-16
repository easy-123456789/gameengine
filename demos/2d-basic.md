# 2D — Basic Demo

A minimal 2D demo showing a colored player sprite that you can move left/right and make jump with Space. Replace `path/to/gameengine.js` with your built engine file.

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8"/>
  <title>gameengine — 2D Basic Demo</title>
  <style>html,body{height:100%;margin:0;background:#0f1720}canvas{display:block;margin:0 auto}</style>
</head>
<body>
  <script src="path/to/gameengine.js"></script>
  <script>
    // Create the game and canvas
    const game = new Game();
    game.initCanvas(800, 480);

    // Create a player sprite
    const player = new Sprite(48, 48, { color: "#4c83e6" })
      .setPosition(100, 380);

    // ground
    const ground = new Sprite(800, 80, { color: "#2a2f36" }).setPosition(400, 440);

    // Add to the game
    game.add(ground);
    game.add(player);

    // Simple physics state on the sprite
    player.vy = 0;
    const gravity = 1200; // px/s^2
    const jumpVelocity = -420;

    // Register key controls (left/right + jump)
    game.registerKey({ key: ["ArrowLeft", "a"], callback: (dt) => player.move(-240 * dt, 0) });
    game.registerKey({ key: ["ArrowRight", "d"], callback: (dt) => player.move(240 * dt, 0) });
    game.registerKey({ key: [" "], callback: (dt, g) => {
      // only allow jump when near ground (simple)
      if (player.y + player.height/2 >= ground.y - 40) {
        player.vy = jumpVelocity;
      }
    }});

    // Start the loop with update + render
    game.start({
      update: (dt, g) => {
        // apply gravity
        player.vy += gravity * dt;
        player.move(0, player.vy * dt);

        // simple floor collision
        const floorY = ground.y - ground.height/2 - player.height/2 + 4;
        if (player.y > floorY) {
          player.y = floorY;
          player.vy = 0;
        }

        // clamp to canvas
        player.clampTo(g.canvas.width, g.canvas.height);
      },
      render: (ctx, g) => {
        // draw a ground line (optional)
        ctx.fillStyle = "rgba(255,255,255,0.03)";
        ctx.fillRect(0, g.canvas.height - 80, g.canvas.width, 80);
      },
      background: "#0b1520"
    });
  </script>
</body>
</html>
```

Notes:
- This demo uses only colored shapes so no external assets or CORS needed.
- Replace the script src with your built JS or module import if used as an ES module.
