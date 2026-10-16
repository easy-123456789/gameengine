# gameengine — 2D and 3D quick reference

A small, dependency-free game engine providing both 2D (canvas 2D) and 3D (WebGL) primitives, a simple animation loop, asset loading, input handling and helper classes for sprites and platformers.

This README contains quick-start examples, API reference for the exported classes and tips for common tasks.

---

## Quick Start — 2D

```html
<!-- include your script, or import it as a module depending on your build -->
<script src="path/to/gameengine.js"></script>
```

```js
// Create the game and a canvas
const game = new Game();
game.initCanvas(800, 600);

// Create a player sprite
const player = new Sprite(48, 48, { color: "#4c83e6" })
  .setPosition(40, 300);

// Add to the game
game.add(player);

// Start a simple loop
game.start({
  update: (dt, g) => {
    // game logic
  },
  render: (ctx, g) => {
    // custom rendering before built-in draw
  },
  background: "#142230"
});
```

Platformer example (2D):
```js
game.startPlatformer({
  player,
  platforms: [ /* Sprite objects used as platforms */ ],
  enemies: [ /* Sprite enemies */ ],
  spawn: { x: 40, y: 300 },
  onUpdate: (state) => console.log(state),
  onEnd: (won, result) => console.log("ended", won, result)
});
```

---

## Quick Start — 3D (WebGL)

```js
const game3d = new Game3D();
game3d.initCanvas(1024, 768); // creates and appends canvas

// Create a 3D sprite (box)
const hero3d = new Sprite3D(1, 1, 1, { x: 0, y: 1, z: 0, color: "#ffcc00" });
game3d.add(hero3d);

// Load textures / images
game3d.loadAssets({
  tile: "/assets/tile.png",
  hero: { src: "/assets/hero.png", fallback: "/assets/hero-fallback.png" }
}).then(() => {
  // textures created automatically once WebGL is initialized
});

// Start the main loop
game3d.start({
  update: (dt, g) => {
    // update camera or objects
  },
  render: (gl, g) => {
    // optional custom WebGL rendering
  },
  background: "#18202b"
});
```

3D platformer helper:
```js
game3d.startPlatformer3D({
  player: hero3d,
  platforms: [ /* Sprite3D platforms */ ],
  enemies: [ /* Sprite3D enemies */ ],
  spawn: { x: 0, y: 2, z: 0 },
  onUpdate: (state) => {},
  onEnd: (won, result) => {}
});
```

---

## API reference — core concepts

### Shared input / keyboard
- Classes install a singleton input handler on first construction:
  - Game.installInput() and Game3D.installInput()
- Key state live in:
  - Game.keys (2D) and Game3D.keys (3D)
- Register callbacks per normalized key:
  - game.registerKey({ key, callback }) or game3d.registerKey({ key, callback })
  - `key` may be a string or an array of strings. Callbacks are called each frame with signature (delta, gameInstance).

Notes:
- Arrow keys are prevented from scrolling when the canvas is focused.
- Normalization:
  - Game3D.normalizeKey: lowercases the key string.
  - Game.normalizeKey: returns single-character keys as lower-case, otherwise returns the key unchanged.

---

### 2D: Game
Important methods:
- constructor(type = "game") — create instance.
- initCanvas(width, height) — create & append a canvas and set context.
- setCanvas(canvas) — attach an existing canvas.
- background(color) — sets canvas CSS background.
- loadAssets(sources) — loads images, signature: { name: src | { src, fallback } }.
  - Returns Promise that resolves when images are loaded.
- add(sprite) — register a Sprite so the engine draws it.
- draw(context) — draws all registered sprites with optional image assets.
- start({ update, render, afterDraw, background }) — starts animation loop.
- startPlatformer({...}) — simple 2D platformer logic (player, platforms, enemies, physics).
- stop() — stops animation.

Game.start loop:
- Calls keys listeners, update, clears canvas, applies background (string or function), calls render, built-in draw(), afterDraw, and schedules next frame via requestAnimationFrame.

### 2D: Sprite
Constructor: new Sprite(width, height, options)
Key properties:
- x, y, width, height, angle, originX/originY
- asset (string key referencing loaded asset), image (Image object), color, stroke, radius, text, font
- colliderWidth/colliderHeight, offsetX/offsetY, boundingColliderUpdater (custom collider logic)
Key methods:
- setPosition(x, y), move(dx, dy)
- rotate(radians), setAngle(radians)
- draw(context, options) — draws image if available or falls back to shape drawing
- getBoundingBox(), collidesWith(other), clampTo(width, height)

---

### 3D: Game3D
Important methods:
- constructor() — creates instance and installs input.
- initCanvas(width, height) — create and append canvas and initialize WebGL.
- setCanvas(canvas) — attach an existing canvas and initializes WebGL context.
- _initRenderer() — internal: compiles shaders, creates buffers, creates textures for any already-loaded assets.
- loadAssets(sources) — loads images and creates GPU textures when GL is ready. Accepts { name: url | { src, fallback } }.
- add(sprite) — registers Sprite3D objects for drawing.
- draw(options) — draws registered heroes using the shader/program. Accepts camera, width, height overrides.
- drawInset({...}) — draws a sub-viewport (useful for minimaps).
- start({ update, render, afterDraw, background }) — starts WebGL-based animation loop.
- startPlatformer3D({...}) — built-in 3D platformer logic (player, platforms, enemies, physics)
- stop() — stops animation.

Notes:
- WebGL shader pipeline is included (vertex + fragment). Mesh used is a unit cube (cubeVertices).
- Textures are created with REPEAT wrap and LINEAR filters.
- The engine uses its own math helpers: identity(), multiply(), modelMatrix(), perspective(), lookAt().

### 3D: Sprite3D
Constructor: new Sprite3D(width, height, depth, options)
Properties:
- x, y, z, width, height, depth
- color, asset (texture key), uvScale, rotationX/Y/Z
- colliderWidth/Height/Depth, offsetX/Y/Z
Methods:
- setPosition(x,y,z), move(dx,dy,dz)
- setRotation(x,y,z), rotate(x,y,z), setAngle(radians)
- getBoundingBox(), collidesWith(other), clampTo(width,height,depth)

---

## Examples and patterns

Loading assets with fallback:
```js
game.loadAssets({
  player: "/assets/player.png",
  tiles: { src: "/assets/tiles.png", fallback: "/assets/tiles-fallback.png" }
}).then(() => {
  // assets available as game.assets.player or game.assets['tiles']
});
```

Registering keyboard handlers:
```js
game.registerKey({
  key: ["ArrowLeft", "a"],
  callback: (dt, g) => { /* called if either key is down */ }
});
```

Using camera-relative movement (3D platformer option):
- In Game3D.startPlatformer3D pass cameraRelativeMovement: true to move the player relative to camera forward direction.

Mini-map or inset rendering (3D):
- Use drawInset({ x, y, width, height, camera, background, border, borderColor }) to render an inset viewport with its own camera.

---

## Implementation notes & gotchas

- CORS: Images used by WebGL must be loaded with crossOrigin = "anonymous" and served with appropriate CORS headers for texImage2D to work.
- Key normalization: 2D Game treats single-character keys as lower-case and leaves other keys unchanged. 3D Game lowercases all keys. Be consistent when registering keys.
- Frame delta cap: Both loop implementations cap delta to 0.05s to protect physics from large jumps.
- 3D modelMatrix rotation order: rz * ry * rx * scale * translation — be mindful when rotating.
- The small cube geometry is centered at (-0.5..0.5) and modelMatrix positions using sprite center offsets.

---

## Where to go next

- Add helper primitives (plane, sphere) and indexed geometry to reduce vertex count.
- Add a simple scene graph / hierarchical transforms for parent/child objects.
- Expose shader hooks or material system to support lighting variations and normal maps.

---

If you'd like, I can:
- Commit this README.md to the repository (create a branch and PR).
- Generate example HTML demos for the 2D and 3D platformers.
- Add TypeScript declarations for better IDE autocompletion.
