---
title: phaser调试
---

### debug input

[在线demo][1]

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create,
  update: update,
  render: render
});

var sprite;
var counter = 0;
var step = Math.PI * 2 / 360;

function preload() {
  game.load.image('sprite', '../assets/sprites/phaser2.png');
}

function create() {
  sprite = game.add.sprite(0, 0, 'sprite');
  sprite.alpha = 0.5;
  sprite.x = game.width / 2;
  sprite.anchor.set(0.5);
  sprite.inputEnabled = true;
}

function update() {
  var tStep = Math.sin(counter);
  sprite.y = (game.height / 2) + tStep * 30;
  sprite.rotation += Phaser.Math.degToRad(0.1 * tStep);
  counter += step;
}

function render() {
  game.debug.inputInfo(32, 32);
  game.debug.spriteInputInfo(sprite, 32, 130);
  game.debug.pointer(game.input.activePointer);
}
```

### debug sprite

[在线demo][2]

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create,
  update: update,
  render: render
});

var sprite;
var counter = 0;
var step = Math.PI * 2 / 360;

function preload() {
  game.load.image('sprite', '../assets/sprites/phaser2.png');
}

function create() {
  sprite = game.add.sprite(0, 0, 'sprite');
  sprite.alpha = 0.5;
  sprite.x = game.width / 2;
  sprite.anchor.set(0.5);
}

function update() {
  var tStep = Math.sin(counter);
  sprite.y = (game.height / 2) + tStep * 30;
  sprite.rotation += Phaser.Math.degToRad(0.1 * tStep);
  counter += step;
}

function render() {
  game.debug.spriteInfo(sprite, 32, 32);
}
```

### debug physics

[在线demo][3]

```
var game = new Phaser.Game(800, 600, Phaser.CANVAS, '', {
  preload: preload,
  create: create,
  update: update,
  render: render
});

var sprite;
var counter = 0;
var step = Math.PI * 2 / 360;

function preload() {
  game.load.image('sprite', '../assets/sprites/phaser2.png');
}

function create() {
  game.physics.startSystem(Phaser.Physics.ARCADE);
  sprite = game.add.sprite(game.world.centerX, game.world.centerY, 'sprite');
  sprite.anchor.set(0.5);
  game.physics.arcade.enable(sprite);
}

function update(){
  var tStep = Math.sin(counter);
  sprite.body.y = 120 + tStep * 60;
  counter += step;
}

function render() {
  game.debug.body(sprite);
}
```

### debug camera

[在线demo][4]

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create,
  update: update,
  render: render
});

var sprite, spriteB;
var counter = 0;
var step = Math.PI * 2 / 360;

function preload() {
  game.load.image('sprite', '../assets/sprites/phaser2.png');
}

function create() {
  sprite = game.add.sprite(0, 0, 'sprite');
  sprite.alpha = 0.5;
  sprite.x = game.width / 2;
  sprite.anchor.set(0.5);
}

function update() {
  var tStep = Math.sin(counter);
  sprite.y = (game.height / 2) + tStep * 30;
  sprite.rotation += Phaser.Math.degToRad(0.1 * tStep);
  counter += step;
}

function render() {
  game.debug.cameraInfo(game.camera, 32, 32);
}
```

### debug display

[在线demo][5]

```

var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create,
  update: update,
  render: render
});

var sprite;
var counter = 0;
var step = Math.PI * 2 / 360;

function preload() {
  game.load.image('sprite', '../assets/sprites/phaser2.png');
}

function create() {
  sprite = game.add.sprite(0, 0, 'sprite');
  sprite.alpha = 0.5;
  sprite.x = game.width / 2;
  sprite.anchor.set(0.5);
  sprite.inputEnabled = true;
}

function update() {
  var tStep = Math.sin(counter);
  sprite.y = (game.height / 2) + tStep * 30;
  sprite.rotation += Phaser.Math.degToRad(0.1 * tStep);
  counter += step;
}

function render() {
  game.debug.spriteBounds(sprite);
  // game.debug.spriteCorners(sprite, true, true);
}
```
[5]: http://qiudeqing.com/learn-phaser/example/debug/05.html
[4]: http://qiudeqing.com/learn-phaser/example/debug/04.html
[3]: http://qiudeqing.com/learn-phaser/example/debug/03.html
[2]: http://qiudeqing.com/learn-phaser/example/debug/02.html
[1]: http://qiudeqing.com/learn-phaser/example/debug/01.html
