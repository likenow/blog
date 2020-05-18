---
title: 用Phaser来制作一个html5游戏
date: 2015-06-06 19:52:53
categories:
- 前端
tags:
- Phaser
- 游戏
---

## 背景

到目前为止，我的毕业设计已经有了初稿。实际搞了个什么东西呢？没有去搞什么图书管理系统、仓库管理系统、服装管理系统。。。。我搞了一个网页小游戏。为什么呢？毕竟有四年的计算机专业素养，临到毕业了总不能老正以前的那一套。但是我发现上课学的那点儿东西像`C`、`Java`以及`SQL Server`等等貌似做不出啥项目来。赶时髦看的那点儿`OC`根本开发不了项目。倒是在前端开发方面，平时倒是能够干点儿小活。`Javascript`/`jQuery`、`HTML`、`CSS`这些知识也能够运用到开发上。然后恰好看到了老外的一个开源的游戏开发框架**Phaser**，所以就动手搞起来。

搞起来发现，**Phaser** 这个框架真的比较容易使用，封装度比较高，即使没接触过游戏开发，通过官方的文档和一些优秀的Demo作品，也可以快速的做一个小游戏。好了，闲话不多说，咱们直接开始。

<!--more-->

## 使用Phaser 制作一款简易的 Flappy Bird

- [How to Make Flappy Bird in HTML5 With Phaser - Part 1](http://www.lessmilk.com/tutorial/flappy-bird-phaser-1)

- [How to Make Flappy Bird in HTML5 With Phaser - Part 2](http://www.lessmilk.com/tutorial/flappy-bird-phaser-2)

完全参考上述两篇文章。

### 素材准备

- 图片
- 音效

### 框架和知识储备

- Phaser
- Javascript
- HTML/CSS
- PhotoShop

### 开始写代码

```html
<!--index.html-->
<!DOCTYPE html>  
<html>
    <head>  
        <meta charset="utf-8" />
        <title> Flappy Bird Clone </title>
        <script type="text/javascript" src="phaser.min.js"></script>
        <script type="text/javascript" src="main.js"></script>
    </head>

    <body>

    </body>  
</html>  
```



```javascript
// main.js

// Create our 'main' state that will contain the game
var mainState = {
    preload: function() { 
        // This function will be executed at the beginning     
        // That's where we load the images and sounds 
    },

    create: function() { 
        // This function is called after the preload function     
        // Here we set up the game, display sprites, etc.  
    },

    update: function() {
        // This function is called 60 times per second    
        // It contains the game's logic   
    },
};

// Initialize Phaser, and create a 400px by 490px game
var game = new Phaser.Game(400, 490);

// Add the 'mainState' and call it 'main'
game.state.add('main', mainState); 

// Start the state to actually start the game
game.state.start('main');

```

上述，Phaser 提供的 API

```javascript
	preload: function() { 
        // This function will be executed at the beginning     
        // That's where we load the images and sounds 
    },

    create: function() { 
        // This function is called after the preload function     
        // Here we set up the game, display sprites, etc.  
    },

    update: function() {
        // This function is called 60 times per second    
        // It contains the game's logic   
    }
```

#### 把我们的主角加进去

```javascript
preload: function() { 
    // Load the bird sprite
    game.load.image('bird', 'assets/bird.png'); 
},

create: function() { 
    // Change the background color of the game to blue
    game.stage.backgroundColor = '#71c5cf';

    // Set the physics system
    game.physics.startSystem(Phaser.Physics.ARCADE);

    // Display the bird at the position x=100 and y=245
    this.bird = game.add.sprite(100, 245, 'bird');

    // Add physics to the bird
    // Needed for: movements, gravity, collisions, etc.
    game.physics.arcade.enable(this.bird);

    // Add gravity to the bird to make it fall
    this.bird.body.gravity.y = 1000;  

    // Call the 'jump' function when the spacekey is hit
    var spaceKey = game.input.keyboard.addKey(
                    Phaser.Keyboard.SPACEBAR);
    spaceKey.onDown.add(this.jump, this);     
},

update: function() {
    // If the bird is out of the screen (too high or too low)
    // Call the 'restartGame' function
    if (this.bird.y < 0 || this.bird.y > 490)
        this.restartGame();
},
```

#### 提供两个函数

```javascript
// Make the bird jump 
jump: function() {
    // Add a vertical velocity to the bird
    this.bird.body.velocity.y = -350;
},

// Restart the game
restartGame: function() {
    // Start the 'main' state, which restarts the game
    game.state.start('main');
},
```

#### 把障碍物，即管道加进去

在 `preload`方法中加入如下代码：

```javascript
game.load.image('pipe', 'assets/pipe.png');
```



```javascript
addOnePipe: function(x, y) {
    // Create a pipe at the position x and y
    var pipe = game.add.sprite(x, y, 'pipe');

    // Add the pipe to our previously created group
    this.pipes.add(pipe);

    // Enable physics on the pipe 
    game.physics.arcade.enable(pipe);

    // Add velocity to the pipe to make it move left
    pipe.body.velocity.x = -200; 

    // Automatically kill the pipe when it's no longer visible 
    pipe.checkWorldBounds = true;
    pipe.outOfBoundsKill = true;
},
addRowOfPipes: function() {
    // Randomly pick a number between 1 and 5
    // This will be the hole position
    var hole = Math.floor(Math.random() * 5) + 1;

    // Add the 6 pipes 
    // With one big hole at position 'hole' and 'hole + 1'
    for (var i = 0; i < 8; i++)
        if (i != hole && i != hole + 1) 
            this.addOnePipe(400, i * 60 + 10);   
},

```

实际游戏中，加障碍管道是随机的，而且伴随着小鸟的前行，不断变化，所以：

```javascript
this.timer = game.time.events.loop(1500, this.addRowOfPipes, this); 
```

我们通过时钟，每1.5秒就调用一次 `addRowOfPipes`方法。



#### 计分达到规定值通关和处理碰撞到障碍物游戏结束

在`creat()`方法中加入如下代码：

```javascript
this.score = 0;
this.labelScore = game.add.text(20, 20, "0", 
    { font: "30px Arial", fill: "#ffffff" });   
```

在  `addRowOfPipes`方法中加入如下代码，表示，没创建一个障碍物就加一分：

```javascript
this.score += 1;
this.labelScore.text = this.score;  
```

在 `update`方法中，加入如下代码，以在主角碰到障碍物时结束游戏，重新开始

```javascript
game.physics.arcade.overlap(
    this.bird, this.pipes, this.restartGame, null, this);
```

到目前为止，我们已经实现了一个简单的，不加修饰的 Flappy Bird 游戏。然而，这显然不能够满足我的要求。

#### 添加飞行动画效果

 参考原作，我们发现：

1. 鸟慢慢向下旋转，直到某一点

2. 当鸟跳跃时，它会向上旋转

第一点，我们可以通过以下代码实现：

```javascript
if (this.bird.angle < 20)
    this.bird.angle += 1; 
```

第二点，我们可以如下试下：

```javascript
game.add.tween(this.bird).to({angle: -20}, 100).start(); 
```

或者如下三行代码

```javascript
// Create an animation on the bird
var animation = game.add.tween(this.bird);

// Change the angle of the bird to -20° in 100 milliseconds
animation.to({angle: -20}, 100);

// And start the animation
animation.start(); 
```

我们需要做是改变鸟的旋转中心，称为“锚”。 所以我们在`create`方法中添加这行：

```javascript
 // Move the anchor to the left and downward
this.bird.anchor.setTo(-0.2, 0.5); 
```

#### 添加鸟碰到障碍物挂掉的动画

```javascript
hitPipe: function() {
    // If the bird has already hit a pipe, do nothing
    // It means the bird is already falling off the screen
    if (this.bird.alive == false)
        return;

    // Set the alive property of the bird to false
    this.bird.alive = false;

    // Prevent new pipes from appearing
    game.time.events.remove(this.timer);

    // Go through all the pipes, and stop their movement
    this.pipes.forEach(function(p){
        p.body.velocity.x = 0;
    }, this);
}, 
```

如果，鸟碰到障碍物就会从空中掉落，所以在`update`方法中修改代码如下：

```javascript
game.physics.arcade.overlap(
    this.bird, this.pipes, this.hitPipe, null, this);  
```

然后，在`jump`方法中加上如下代码：

```javascript
if (this.bird.alive == false)
    return;  
```

#### 别忘了我我们一开始准备的音效

在 `preload()` 方法中加入如下代码，载入音效

```javascript
game.load.audio('jump', 'assets/jump.wav'); 
```

在 `create()` 方法加入如下代码：

```javascript
this.jumpSound = game.add.audio('jump'); 
```

最后在 `jump()` 方法中加入这行，播放音效的代码：

```javascript
this.jumpSound.play(); 
```

到目前为止，我们已经完成了练手项目。效果见[Phaser Demo](https://github.com/likenow/gp)



## 参考文献

- [How to Make Flappy Bird in HTML5 With Phaser - Part 1](http://www.lessmilk.com/tutorial/flappy-bird-phaser-1)

- [How to Make Flappy Bird in HTML5 With Phaser - Part 2](http://www.lessmilk.com/tutorial/flappy-bird-phaser-2)

- [Game Development Tutorials](http://www.lessmilk.com/)
- [Phaser](http://phaser.io/)

- [Phaser Docs](http://phaser.io/docs)
