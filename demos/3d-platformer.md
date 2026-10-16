# 3D — Platformer Demo

A simple 3D platformer wiring with the engine's startPlatformer3D helper. This demo places a player and a few platforms and enables camera-relative movement.

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8"/>
  <title>gameengine — 3D Platformer Demo</title>
  <style>html,body{height:100%;margin:0;background:#071018}canvas{display:block;margin:0 auto}</style>
</head>
<body>
  <script src="path/to/gameengine.js"></script>
  <script>
    const game3d = new Game3D();
    game3d.initCanvas(1200, 700);

    // Player cube
    const player = new Sprite3D(1, 1.6, 1, { x: 0, y: 1.6/2 + 0.05, z: 0, color: "#ffd166" });
    game3d.add(player);

    // Platforms
    const platforms = [
      new Sprite3D(30, 1, 30, { x: 0, y: -0.5, z: 0, color: "#263238" }),  // ground
      new Sprite3D(6, 0.5, 6, { x: 5, y: 0.6, z: -4, color: "#2a6f97" }),
      new Sprite3D(4, 0.5, 4, { x: -6, y: 1.4, z: -8, color: "#2a6f97" }),
      new Sprite3D(2.5, 0.5, 2.5, { x: 2, y: 2.6, z: -12, color: "#2a6f97" }),
    ];
    platforms.forEach(p => game3d.add(p));

    // Start the 3D platformer. cameraRelativeMovement makes controls follow camera forward.
    game3d.startPlatformer3D({
      player,
      platforms,
      enemies: [],
      spawn: { x: 0, y: 1, z: 0 },
      cameraRelativeMovement: true,
      onUpdate: (state) => {
        // you can inspect the `state` in the browser console
      },
      onEnd: (won, result) => {
        alert(won ? "Victory!" : "Game Over");
      }
    });
  </script>
</body>
</html>
```

Notes:
- Serve demo over HTTP(S).
- If you replace color materials with textures, use game3d.loadAssets(...) before calling startPlatformer3D and ensure images are served with CORS headers.
