<DOCTYPE!html>
<html>
<title> Crappy Bird </title>
<style>

body {
	background:black
   }

.start {
	text-align: center;
	margin: auto;
	display: block;
	width: 200;
	height: 100;
	position: absolute;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
}

canvas {
	 padding: 0;
	margin: auto;
	display: block;
	width: 800px;
	height: 600px;
	position: absolute;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	z-index: 2;
	background-image: url('https://healeycodes.com/_next/image?url=%2Fposts%2Fflappy-bird%2Fflappy-bird.gif&w=640&q=100');
	background-size: 800px;
	border:4px outset red;
   }

.container {
	display: flex;
	align-items: center;
	justify-content: center;
}

</style>

<body id="maybe">

<div class="start">
<button 
	type="button"
	id="retry"
	style="font-family: times new roman; font-size: 40; color: purple; background-color:grey; border-color:red;"
	onclick= "startGame()"> 
	Start Game
</button>
</div>

<h1 
	class = "container"
	id="loss"
	style="font-family: comic sans ms; color: magenta; font-size: 50; ">
</h1>

<h2 
	class = "container"
	id="high" 
	style="color:white;  font-size: 40;">
 
</h2>

<h3
	class = "container"
	id="scor"
	style="color:white; ; font-size: 30; height: 100;">

</h3>

<script>
var canvasWidth = 1000;
var canvasHeight = 500;
var player;
var playerYPosition = 500;
var isJumping = false;
var jumpSpeed = -7;
var fallSpeed = 0.5;
var block;
var bird;
var audio = new Audio('https://www.myinstants.com/media/sounds/wide-putin-walking-but-hes-always-in-frame-full-version.mp3');
var audio2 = new Audio('https://www.myinstants.com/media/sounds/metal-pipe-clang.mp3');
var audio3 = new Audio('https://www.myinstants.com/media/sounds/nfl.mp3');
var score = 0;
var highScore = localStorage.getItem('highScore') || 0;
var scoreLabel;
var defaultColor = "purple";
var interval = setInterval(updateCanvas, 20);
document.getElementById('high').innerHTML = "High Score: " + highScore

	function startGame() {
		gameCanvas.start();
		player = new createPlayer(30, 30, 30);
		block = new createBlock();
		bird = new createBird();
		scoreLabel = new createScoreLabel(10, 20);
		document.getElementById('canvas').style.border = "4px outset red"
		document.getElementById('canvas').style.height = "500"
		document.getElementById('canvas').style.width = "1000"
		document.getElementById('loss').innerHTML = ""
		document.getElementById('retry').innerHTML = ""
		audio.play();
		audio.loop = true;
		audio2.pause();	
		audio3.pause();
		document.getElementById('high').innerHTML = ""
		document.getElementById('scor').innerHTML = ""
		score = 0;
   }

var gameCanvas = {
	canvas: document.createElement("canvas"),
	start: function() {
		this.canvas.width = canvasWidth;
		this.canvas.height = canvasHeight;
		this.canvas.id = "canvas";
		this.context = this.canvas.getContext("2d");
		document.body.insertBefore(this.canvas, document.body.childNodes[0]);
	}
   }

	function stopAudio() {
		audio.volume = 0.0;
		audio2.volume = 0.0;;
		audio3.volume = 0.0;;
}
	function startAudio() {
		audio.volume = 1.0;
		audio2.volume = 1.0;
		audio3.volume = 1.0;
}	

	function createBlock() {
	   var width = randomNumber(30, 75);
	   var height = randomNumber(50, 150);
	   var speed = randomNumber(6, 9);

		this.x = canvasWidth;
		this.y = canvasHeight - height;


	this.draw = function() {
		ctx = gameCanvas.context;
		ctx.fillStyle = "green";
		ctx.fillRect(this.x, this.y, width, height);
   }
  
	

	this.attackPlayer = function() {
		this.x -= speed;
		this.returnToAttackPosition();
    }
	this.returnToAttackPosition = function() {
		if (this.x + width < 0) {
			width = randomNumber(30, 75);
			height = randomNumber(100, 300);
			speed = randomNumber(6, 9);
			this.y = canvasHeight - height;
			this.x = canvasWidth;
			ensureDistance();
			score++;
			
     }
   }
		function ensureDistance() {
		var minDistance = 100; // Minimum distance between block and bird
		if (Math.abs(this.x - bird.x) < minDistance) {
			this.x = bird.x + minDistance;
     }
   }
 } 

	function createBird() {
	   var initialWidth = randomNumber(30, 75);
	   var initialHeight = randomNumber(50, 150);
	   var speed = randomNumber(6, 9);

		this.x = canvasWidth;
		this.y = 0;
		this.width = initialWidth; 
		this.height = initialHeight; 

	this.draw = function() {
        	ctx = gameCanvas.context;
        	ctx.fillStyle = "green";
        	ctx.fillRect(this.x, this.y, this.width, this.height);
    }

	this.attackPlayer = function() {
        	this.x -= speed;
		this.returnToAttackPosition();
    }

	this.returnToAttackPosition = function() {
        	if (this.x + initialWidth < 0) {
			this.width = randomNumber(30, 75);
			this.height = randomNumber(50, 300);
			this.speed = randomNumber(6, 9);
			this.y = 0;
			this.x = canvasWidth;
			ensureDistance();
        }
    }
		function ensureDistance() {
		var minDistance = 100; // Minimum distance between bird and block
		if (Math.abs(this.x - block.x) < minDistance) {
			this.x = block.x + minDistance;
    }
  }
}

	function createPlayer(width, height, x) {
		this.width = width;
		this.height = height;
		this.x = x;
		this.y = playerYPosition;

	this.changeColor = function(color) {
		this.color = color;
   }

	this.color = defaultColor;

	this.draw = function() {
		ctx = gameCanvas.context;
		ctx.fillStyle = this.color;
		ctx.fillRect(this.x, this.y, this.width, this.height);
		ctx.fillStyle = "black";
		ctx.fillRect(this.x + 23, this.y + 1, 7, 7);
		ctx.fillStyle = "white";
		ctx.fillRect(this.x + 17, this.y + 1, 7, 7);
		ctx.fillStyle = "yellow";
		ctx.fillRect(this.x + 30, this.y + 10, 20, 10);
		ctx.fillStyle = "violet";
		ctx.fillRect(this.x + 5, this.y + 15, 13, 7);
   }

	this.makeFall = function() {
		if (!isJumping) {
			this.y += fallSpeed;
			fallSpeed += 0.4;
			this.stopPlayer();
   }
  }

	this.stopPlayer = function() {
		var ground = 477 - this.height;
		if (this.y > ground) {
			this.y = ground;
   }
  }

	this.noFly = function() {
		if (this.y < 0) {
			this.y = 0;
   }
 }

	this.jump = function() {
		if (isJumping && jumpSpeed < 0) {
			this.y += jumpSpeed - 0.4;
			jumpSpeed += 0.3;
			fallSpeed = 0;
		} else {
			isJumping = false;
			 
			
   }
  }
 }

	function detectCollision() {

	   var playerLeft = player.x;
	   var playerRight = player.x + player.width;
	   var playerBottom = player.y + player.height;
	   var playerTop = player.y;
	
	   var blockLeft = block.x
	   var blockRight = block.x + block.width
	   var blockTop = block.y
	
		if (playerRight > blockLeft &&
	        playerLeft < blockLeft &&
 	        playerBottom > blockTop &&
		score < 100) {
			loseGame();
    }
  }

	function deathBird() {

	   var playerLeft = player.x;
	   var playerRight = player.x + player.width;
	   var playerBottom = player.y + player.height;
	   var playerTop = player.y;

	   var birdLeft = bird.x;
	   var birdRight = bird.x + bird.width;
	   var birdBottom = bird.y + bird.height;
	   var birdTop = bird.y;

		if (playerRight > birdLeft &&
	        playerLeft < birdRight &&
	        playerBottom > birdTop &&
	        playerTop < birdBottom &&
		score < 100) {
			loseGame();
    }
  }
	
	function loseGame() {
		document.getElementById('retry').innerHTML = "try again?"
		document.getElementById('loss').innerHTML = "YOU LOSE! HAHAHA"
		audio.pause();
		document.getElementById('canvas');
		canvas.remove();
		audio2.play();
		
		if (score > highScore) {
		 highScore = score;
		 localStorage.setItem('highScore', highScore);
	}
		document.getElementById('high').innerHTML = "High Score: " + highScore
		document.getElementById('scor').innerHTML ="Previous Score: " + score
		score = 0;
}

	function createScoreLabel(x, y) {
		
		this.x = x;
		this.y = y;
		this.draw = function() {
	        	ctx = gameCanvas.context;
	       		ctx.font = "25px Comic Sans Ms";
	       		ctx.fillStyle = "black";
	        	ctx.fillText("SCORE: " + score, this.x, this.y);
			ctx.fillText("HIGH SCORE: " + highScore, 775, this.y);
			if (score == 100) {
			winGame();
	     }
  	}
   }

	


	function winGame() {
		audio.pause();
		audio3.play();
		document.getElementById('canvas');
		canvas.remove();
		document.getElementById('retry').innerHTML = "Play Again"
		document.getElementById('loss').innerHTML = "YOU... Win? dang good job!"
		document.getElementById('loss').style.left = 400

		highScore = 100;
		localStorage.setItem('highScore', highScore);
		document.getElementById('high').innerHTML = "High Score: " + highScore
   }
	


	function updateCanvas() {
		detectCollision();
		deathBird();
	
		ctx = gameCanvas.context;
		ctx.clearRect(0, 0, canvasWidth, canvasHeight);
		
		player.draw();
		player.jump();
		player.makeFall();
		
		block.draw();
		block.attackPlayer();
		bird.draw();
		bird.attackPlayer();
	
		player.noFly();
		player.stopPlayer();

		scoreLabel.text = "SCORE: " + score;
		scoreLabel.draw();
   }

	


	function randomNumber(min, max) {
		return Math.floor(Math.random() * (max - min + 1) + min)
   }




	function resetJump() {
		jumpSpeed = 0;
		
   }
	
	function changeColor() {
		var colors = ["red", "blue", "green", "orange", "yellow", "purple", "cyan", "black", "white", "lime", "salmon", "pink"];
    		var randomColor = colors[Math.floor(Math.random() * colors.length)];
		  player.changeColor(randomColor);
		 defaultColor = randomColor;
}

	
		

	document.addEventListener('mousedown',  function(e) {
		if (e.button == 0 && !isJumping) {
		jumpSpeed = -7;
		isJumping = true;
		
   }
  });

	document.addEventListener('keydown',  function(e) {
		if (e.keyCode == 32 && !isJumping) {
		jumpSpeed = -7;
		isJumping = true;
		
   }
  });
	
	document.addEventListener('keydown', function(e) {
		if(e.keyCode == 76) {
		lightMode();
   }
});

	document.addEventListener('keydown', function(e) {
		if(e.keyCode == 68) {
		darkMode();
   }
});

	document.addEventListener('keydown', function(e) {
		if(e.keyCode == 70) {
		stopAudio();
   }
});

	document.addEventListener('keydown', function(e) {
		if(e.keyCode == 78) {
		startAudio();
   }
});

	document.addEventListener('keydown', function(e) {
		if(e.keyCode == 67) {
		changeColor();
   }
});

	function lightMode() {
		document.getElementById('maybe').style.background = "white";
		document.getElementById('high').style.color = "black";	
		document.getElementById('scor').style.color = "black";
}

	function darkMode() {
		document.getElementById('maybe').style.background = "black";
		document.getElementById('high').style.color = "white";
		document.getElementById('scor').style.color = "white";
}


</script>
</body>
</html>
