<script>
	import { BREAKOUT_HIGHSCORES } from './snakeconstants';
	import { onMount } from 'svelte';
	let canvas;
	var rightPressed = false;
	var leftPressed = false;
	var paddleHeight = 8;
	var paddleWidth = 70;
	var paddleX = 0;
	let score = 0;
	let bestScore = 0;

	onMount(() => {
		const ctx = canvas.getContext('2d');
		var ballRadius = 6;
		var x = canvas.width/2;
		var y = canvas.height;
		var dx = 2;
		var dy = -2;
		var brickRowCount = 10;
		var brickColumnCount = 10;
		var brickWidth = 54;
		var brickHeight = 15;
		var brickPadding = 10;
		var brickOffsetTop = 30;
		var brickOffsetLeft = 0;
		var bricks = [];

		// var lives = 3;
		paddleX = (canvas.width-paddleWidth)/2;
		for(var c=0; c<brickColumnCount; c++) {
			bricks[c] = [];
			for(var r=0; r<brickRowCount; r++) {
				bricks[c][r] = { x: 0, y: 0, status: 1 };
			}
		}
		function collisionDetection() {
			for(var c=0; c<brickColumnCount; c++) {
				for(var r=0; r<brickRowCount; r++) {
					var b = bricks[c][r];
					if(b.status == 1) {
						if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
							dy = -dy;
							b.status = 0;
							score++;
							bestScore = bestScore < score ? score : bestScore;
							if(score == brickRowCount*brickColumnCount) {
								alert("YOU WIN, CONGRATS!");
								document.location.reload();
							}
						}
					}
				}
			}
		}
		// function drawLives() {
		// 	ctx.font = "16px Arial";
		// 	ctx.fillStyle = "#0095DD";
		// 	ctx.fillText("Lives: "+lives, canvas.width-65, 20);
		// }
		function drawScore() {
			// ctx.font = "16px Arial";
			// ctx.fillStyle = "#fff";
			// ctx.fillText("Score: "+score, 8, 20);
		}
		function drawBricks() {
			for(var c=0; c<brickColumnCount; c++) {
				for(var r=0; r<brickRowCount; r++) {
					if(bricks[c][r].status == 1) {
						var brickX = (c*(brickWidth+brickPadding))+brickOffsetLeft;
						var brickY = (r*(brickHeight+brickPadding))+brickOffsetTop;
						bricks[c][r].x = brickX;
						bricks[c][r].y = brickY;
						ctx.beginPath();
						ctx.rect(brickX, brickY, brickWidth, brickHeight);
						ctx.fillStyle = "#91C48A";
						ctx.fill();
						ctx.closePath();
					}
				}
			}
		}
		function drawBall() {
			ctx.beginPath();
			ctx.arc(x, y, ballRadius, 0, Math.PI*2);
			ctx.fillStyle = "#b388ff";
			ctx.fill();
			ctx.closePath();
		}
		function drawPaddle() {
			ctx.beginPath();
			ctx.rect(paddleX, canvas.height-paddleHeight, paddleWidth, paddleHeight);
			ctx.fillStyle = "#555";
			ctx.fill();
			ctx.closePath();
		}
		function draw() {
			ctx.clearRect(0, 0, canvas.width, canvas.height);
			drawBricks();
			drawBall();
			drawPaddle();
			drawScore();
			// drawLives();
			collisionDetection();
			if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
				dx = -dx;
			}
			if(y + dy < ballRadius) {
				dy = -dy;
			}
			else if(y + dy > canvas.height-ballRadius) {
				if(x > paddleX && x < paddleX + paddleWidth) {
					dy = -dy;
				}
				// else {
				// 	lives--;
				// 	if(!lives) {
				// 		alert("GAME OVER");
				// 		window.location.reload();
				// 	}
				else {
					x = canvas.width/2;
					y = canvas.height-30;
					dx = 2;
					dy = -2;
					paddleX = (canvas.width-paddleWidth)/2;
					score = 0;
					}
				}
			// }
			if(rightPressed && paddleX < canvas.width-paddleWidth) {
				paddleX += 7;
			}
			else if(leftPressed && paddleX > 0) {
				paddleX -= 7;
			}
			x += dx;
			y += dy;
			requestAnimationFrame(draw);
		}
		draw();
	});

	try {
      bestScore = JSON.parse(localStorage.getItem(BREAKOUT_HIGHSCORES)) || [0];
    } catch (err) {
      bestScore = [0];
    }

	function keyDownHandler(e) {
		if(e.keyCode == 39) {
			rightPressed = true;
		}
		else if(e.keyCode == 37) {
			leftPressed = true;
		}
	}
	function keyUpHandler(e) {
		if(e.keyCode == 39) {
			rightPressed = false;
		}
		else if(e.keyCode == 37) {
			leftPressed = false;
		}
	}
	function mouseMoveHandler(e) {
		var relativeX = e.clientX - canvas.offsetLeft;
		console.log(e.clientX, canvas.offsetLeft, relativeX)
		if(relativeX > 0 && relativeX < canvas.width) {
			paddleX = relativeX - paddleWidth / 2;
		} 
	}	
	$: try {
      localStorage.setItem(BREAKOUT_HIGHSCORES, JSON.stringify(bestScore));
    } catch (err) {
      noop
    }
</script>

<!-- <svelte:window on:keydown={keyDownHandler} on:keyup={keyUpHandler} on:mousemove={mouseMoveHandler}/> -->
<svelte:window on:keydown={keyDownHandler} on:keyup={keyUpHandler}/>

<div class="scores">
	<p>Score: {score}</p>
	<p>High Score: {bestScore}</p>
</div>


<canvas
	bind:this={canvas}
	width={640}
	height={500}
></canvas>

<style>
	/* canvas { background: #eee; } */
	.scores {
		display: flex;
		justify-content: space-between;
	}
</style>