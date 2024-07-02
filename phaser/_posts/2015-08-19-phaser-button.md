---
title: phaser button
---

## 添加按钮并监听

[在线demo][1]

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create
});

function preload() {
  game.load.spritesheet('button', '../assets/buttons/button_sprite_sheet.png', 193, 71);
  game.load.image('background', '../assets/misc/starfield.jpg');
}

var button;
var background;

function create() {
  game.stage.backgroundColor = '#182d3b';
  background = game.add.tileSprite(0, 0, 800, 600, 'background');
  button = game.add.button(game.world.centerX - 95, 400, 'button', actionOnClick, this, 2, 1, 0);

  button.onInputOver.add(over, this);
  button.onInputOut.add(out, this);
  button.onInputUp.add(up, this);
}

function up() {
  console.log('button up ', arguments);
}
function over() {
  console.log('button over ', arguments);
}
function out() {
  console.log('button out ', arguments);
}

function actionOnClick() {
  background.visible = !background.visible;
}
```


[4]: http://qiudeqing.com/learn-phaser/example/button/4.html
[3]: http://qiudeqing.com/learn-phaser/example/button/3.html
[2]: http://qiudeqing.com/learn-phaser/example/button/2.html
[1]: http://qiudeqing.com/learn-phaser/example/button/1.html

