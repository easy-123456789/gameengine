# 3D — Basic Demo (WebGL)

A minimal WebGL demo that creates a cube-like Sprite3D and rotates it. No textures used — colored material only. Serve via HTTP(S) (not file://) to ensure WebGL context works reliably.

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8"/>
  <title>gameengine — 3D Basic Demo</title>
  <style>html,body{height:100%;margin:0;background:#0b1220}canvas{display:block;margin:0 auto}</style>
</head>
<body>
  <script src="path/to/gameengine.js"></script>
  <script>
    const game3d = new Game3D();
    game3d.initCanvas(1024, 640);

    // Create a 3D hero (unit cube scaled by width/height/depth)
    const hero = new Sprite3D(1.4, 1.4, 1.4, { x: 0, y: 1.2, z: 0, color: "#ffcc00" });
    game3d.add(hero);

    // Add a few static platforms (simple cubes)
    const ground = new Sprite3D(20, 1, 20, { x: 0, y: -0.5, z: 0, color: "#263238" });
    const platform = new Sprite3D(4, 0.5, 4, { x: 3, y: 0.5, z: -3, color: "#2a6f97" });
    game3d.add(ground);
    game3d.add(platform);

    // Simple rotation in update
    game3d.start({
      update: (dt, g) => {
        hero.rotationY = (hero.rotationY || 0) + dt * 1.2;
        // small bobbing
        hero.y = 1.2 + Math.sin(Date.now() / 400) * 0.06;
      },
      render: (gl, g) => {
        // optional extra GL calls if needed
      },
      background: "#081220"
    });
  </script>
</body>
</html>
```

Notes:
- This demo uses colored meshes only; no textures so CORS isn't an issue.
- For textured sprites you'll need to call game3d.loadAssets(...) and serve images with correct CORS headers.
