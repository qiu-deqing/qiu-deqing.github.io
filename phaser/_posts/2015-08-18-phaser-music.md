---
title: phaser播放音乐

---

## 播放音乐并调节音量

[在线demo][1]

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create,
  update: update,
  render: render
});

function preload() {
  game.load.image('disk', '../assets/sprites/ra_dont_crack_under_pressure.png');
  game.load.audio('boden', ['../assets/audio/bodenstaendig_2000_in_rock_4bit.mp3',
    '../assets/audio/bodenstaendig_2000_in_rock_4bit.ogg']);
}

var s;
var music;

function create() {
  game.stage.backgroundColor = '#182d3b';
  game.input.touch.preventDefault = false;

  music = game.add.audio('boden');
  music.play();

  s = game.add.sprite(game.world.centerX, game.world.centerY, 'disk');
  s.anchor.setTo(0.5);

  game.input.onDown.add(changeVolumn, this);
}

function changeVolumn(pointer) {
  console.log(pointer.y)
  if (pointer.y < 100) {
    music.mute = false;
  } else if (pointer.y < 300) {
    music.volume += 0.1;
  } else {
    music.volume -= 0.1;
  }
}

function update() {
  s.rotation += 0.001;
}

function render() {
  game.debug.soundInfo(music, 20, 32);
}
```

## fadeIn: 音量逐渐从0增加到1

[在线demo][2]

```
music = game.add.audio('boden');
music.onDecoded.add(start, this);

function start() {
  music.fadeIn(4000);
}
```

[2]: http://qiudeqing.com/learn-phaser/example/audio/2.html
[1]: http://qiudeqing.com/learn-phaser/example/audio/1.html
