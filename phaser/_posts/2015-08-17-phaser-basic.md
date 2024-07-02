---
title: Phaser基础

---

本文对应[Phaser例子中的基础部分][1],按照从简单到复杂进行总结.可以[去github下载全部例子][2]

## 加载图片并显示

[在线demo][3]

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create
});

function preload() {
  game.load.image('einstein', '../assets/pics/ra_einstein.png');
}

funtion create() {
  game.add.sprite(0, 0, 'einstein');
}
```

上面的代码执行以下操作:
1. 新建一个Game对象,设置宽度,高度,自动选择WebGL或者canvas绘图, 游戏容器添加到body, 设置默认state
2. 在默认state的preload函数中加载所需资源,这里是需要绘制的图片
3. 在默认state的create函数中将加载的资源在游戏的0,0坐标绘制出来

## 居中显示图片并监听点击事件

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create
});

var text;
var counter = 0;

function preload() {
  game.load.image('einstein', '../assets/pics/ra_einstein.png');
}
function create() {
  var image = game.add.sprite(game.world.centerX, game.world.centerY, 'einstein');
  image.anchor.set(0.5);
  image.inputEnabled = true;
  text = game.add.text(250, 16, '', {fill: '#fff'});
  image.events.onInputDown.add(listener, this);
}
function listener() {
  ++counter;
  text.text = 'You clicked' + counter + ' times!'
}
```

上面的代码包含两个功能: 1. 图片居中 2. 事件监听 3. 显示文字

图片居中通过设置`image.anchor`指定图片哪个位置放在指定坐标上, 设置为(0.5, 0.5)表示将图片中间点放在指定坐标,从而实现图片居中.

事件监听包括三部分:

1. `image.inputEnabled = true;` 启动事件监听, 系统会转发对应事件到对象.
2. `image.events.onInputDown.add(listener, this);`为对象关心的事件添加监听器
3. 实现listener

[Phaser.Events API][4]

[Phaser.Text API][6]显示文字:

1. `var text = game.add.text(250, 16, '', {fill: '#fff'});` 以需要的样式创建一个文字对象并在制定位置显示
2. `text.text = 'You clicked ' + center + ' times!'`更新文字内容

### 自动移动图片

```
var image = game.add.sprite(0, 0, 'einstein');
game.physics.enable(image, Phaser.Physics.ARCADE);
image.body.velocity.x = 150;
```

自动移动对象需要为对象设置一个Physics body,用于自动控制对象位置并显示.

1. `game.physics.enable(image, Phaser.Physics.ARCADE)`为image添加一个Physics.Arcade.Body对象body, 用于表示对象的运动信息, Phaser.Physics.ARCADE根据它控制对象运动
2. `image.body.velocity.x = 150;`修改对象的physics body属性从而控制对象移动.

[Phaser.Physics APi][7]


### 图片自动向鼠标或最近touch位置移动

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, '', {
  preload: preload,
  create: create,
  update: update,
  render: render
});
function preload() {
  game.load.image('phaser', '../assets/sprites/phaser.png');
}
var sprite;

function create() {
  console.log(game.physics.arcade)
  game.physics.startSystem(Phaser.Physics.ARCADE);
  console.log(game.physics.arcade)

  sprite = game.add.sprite(game.world.centerX, game.world.centerY, 'phaser');
  sprite.anchor.set(0.5);

  game.physics.arcade.enable(sprite);
}

function update() {
  if (game.physics.arcade.distanceToPointer(sprite, game.input.activePointer) > 8) {
    game.physics.arcade.moveToPointer(sprite, 300);
  }
  else {
    sprite.body.velocity.set(0);
  }
}

function render() {
  game.debug.inputInfo(32, 32);
}
```

创建game传递的state对象增加了两个方法, 可以查看Phaser.State查看详细信息,概括如下:
1. `update`: 游戏循环时系统执行debug, Physics, plugins之后调用, 通常在这里添加游戏处理逻辑
2. `render`: 游戏每一轮渲染完成之后调用, 通常在这里添加额外需要的样式效果

create执行操作主要如下
1. `game.physics.startSystem(Phaser.Physics.ARCADE)`: 新建一个Physics系统并应用, 由于系统默认创建了Arcade系统, 再次调用这个函数会reset系统
2. 居中显示图片
3. `game.physics.arcade.enable(sprite)`: 为sprite创建一个Arcade Physics body用于描述运动所需信息

update主要操作如下:

判断图片与`activePointer`的距离
1. 如果大于8, 向activePointer位置移动
2. 如果小于8, 设置sprite移动速度为0

render使用`game.debug.inputInfo(32, 32)`显示调试信息


### 显示文字

```
var text = '- phaser -\n with a sprinkle of \n pixi dust.';
var style = {
  font: '65px Arial',
  fill: '#f04',
  align: 'center'
};

var t = game.add.text(game.world.centerX - 300, 0, text, style);
```

直接添加文字即可. `t.text = newText;`可修改文字内容

### 为元素实现简单动画

```
function create() {
  var sprite = game.add.sprite(-500, 0, 'einstein');
  var tween = game.add.tween(sprite);
  tween.to({
    x: 600
  }, 6000);
  tween.start();
}
```

[7]: http://phaser.io/docs/2.4.2/Phaser.Physics.html
[6]: http://phaser.io/docs/2.4.2/Phaser.Text.html
[5]: http://qiudeqing.com/learn-phaser/example/basic/02.html
[4]: http://phaser.io/docs/2.4.2/Phaser.Sprite.html#events
[3]: http://qiudeqing.com/learn-phaser/example/basic/01.html
[2]: https://github.com/photonstorm/phaser-examples
[1]: http://phaser.io/examples/v2/category/basics
