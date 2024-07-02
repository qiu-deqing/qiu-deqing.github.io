---
title: phaser接收输入
---


## 监听按键并响应

[在线demo][1]

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create,
  update: update
});

function preload() {
  game.load.image('phaser', '../assets/sprites/phaser-dude.png');
}
var sprite;

var upKey;
var downKey;
var leftKey;
var rightKey;

function create() {
  game.stage.backgroundColor = '#736357';
  sprite = game.add.sprite(300, 300, 'phaser');

  upKey = game.input.keyboard.addKey(Phaser.Keyboard.UP);
  downKey = game.input.keyboard.addKey(Phaser.Keyboard.DOWN);
  leftKey = game.input.keyboard.addKey(Phaser.Keyboard.LEFT);
  rightKey = game.input.keyboard.addKey(Phaser.Keyboard.RIGHT);
}

function update() {
  if (upKey.isDown) {
    sprite.y --;
  }
  if (downKey.isDown) {
    sprite.y ++;
  }
  if (leftKey.isDown) {
    sprite.x --;
  }
  if (rightKey.isDown) {
    sprite.x ++;
  }
}
```

## 监听鼠标点击并跟随

[在线demo][2]

```
function create() {
  sprite = game.add.sprite(game.world.centerX, game.world.centerY, 'ball');
  game.physics.enable(sprite, Phaser.Physics.ARCADE);
}

function update() {
  if (game.input.mousePointer.isDown) {
    game.physics.arcade.moveToPointer(sprite, 400);
    if (Phaser.Rectangle.contains(sprite.body, game.input.x, game.input.y)) {
      sprite.body.velocity.setTo(0, 0);
    }
  } else {
    sprite.body.velocity.setTo(0, 0);
  }
}
```

## 监听鼠标点击或者touch

[在线demo][3]

```
function create() {
  game.stage.backgroundColor = '#454645';
  up = game.add.text(20, 20, '');
  down = game.add.text(20, 80, '');
  tap = game.add.text(20, 160, '');
  game.input.onUp.add(function () {
    up.text = 'up';
  });
  game.input.onDown.add(function () {
    down.text = 'down';
  });
  game.input.onTap.add(function () {
    tap.text = 'tap';
  })
}

function render() {
  game.debug.pointer(game.input.mousePointer);
  game.debug.pointer(game.input.pointer1);
  game.debug.pointer(game.input.pointer2);
}
```

## 监听方向键按键移动飞船

[在线demo][4]

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create,
  update: update,
  render: render
});

var ufo;
var leftBtn;
var rightBtn;
var speed = 4;

function preload() {
  game.world.setBounds(0, 0, 1280, 600);

  game.load.image('ground', '../assets/tests/ground-2x.png');
  game.load.image('river', '../assets/tests/river-2x.png');
  game.load.image('sky', '../assets/tests/sky-2x.png');
  game.load.image('cloud0', '../assets/tests/cloud-big-2x.png');
  game.load.image('cloud1', '../assets/tests/cloud-narrow-2x.png');
  game.load.image('cloud2', '../assets/tests/cloud-small-2x.png');

  game.load.spritesheet('button', '../assets/buttons/arrow-button.png', 112, 91);
  game.load.image('ufo', '../assets/sprites/ufo.png');
}

function create() {
  game.add.tileSprite(0, 0, 1280, 600, 'sky');
  game.add.sprite(0, 360, 'ground');
  game.add.sprite(0, 400, 'river');
  game.add.sprite(200, 120, 'cloud0');
  game.add.sprite(-60, 120, 'cloud1');
  game.add.sprite(900, 170, 'cloud2');

  ufo = game.add.sprite(320, 240, 'ufo');
  ufo.anchor.setTo(0.5);

  game.camera.follow(ufo);

  leftBtn = game.add.sprite(160 - 112, 200, 'button', 0);
  leftBtn.alpha = 0;
  rightBtn = game.add.sprite(640 - 112, 200, 'button', 1);
  rightBtn.alpha = 0;
}

function update() {
  if (game.input.keyboard.isDown(Phaser.Keyboard.LEFT)) {
    ufo.x -= speed;
    ufo.angle = -15;
    leftBtn.alpha = 0.5;
  } else if (game.input.keyboard.isDown(Phaser.Keyboard.RIGHT)) {
    ufo.x += speed;
    ufo.angle = 15;
    rightBtn.alpha = 0.6;
  } else {
    ufo.rotation = 0;
    leftBtn.alpha = rightBtn.alpha = 0;
  }
}

function render() {
  game.debug.text('Hold left/right to move the ufo');
}
```


[4]: http://qiudeqing.com/learn-phaser/example/input/4.html
[3]: http://qiudeqing.com/learn-phaser/example/input/3.html
[2]: http://qiudeqing.com/learn-phaser/example/input/2.html
[1]: http://qiudeqing.com/learn-phaser/example/input/1.html
