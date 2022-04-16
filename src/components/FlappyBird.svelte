<script>

  import { FLAPPY_HIGHSCORES } from './snakeconstants';

    import { onMount } from "svelte";
    const interval = 24;
    const canvasSize = 700;
    // Bird Info
    const bird = new Image();
    const birdSize = 45;
    bird.src = "/assets/duck.svg";
    // Bird's speed in y direction
    let birdDy = 0;
    // Bord's speed in x direction
    const birdDx = 0;
    // Bird's X coord
    let birdX = 15;
    // Bird's Y Coord
    let birdY = 200;
    // Pipe Info
    const pipeWidth = 50;
    let pipeX = 500;
    const pipeGap = 200;
    let topPipeBottomY = 200;
    // Score info
    let score = 0;
    let bestScore = 0;
    onMount(() => {
      const game = document.getElementById("game");
      const context = game.getContext("2d");
      setInterval(() => {
        context.fillStyle = "#42a5f5";
        context.fillRect(0, 0, canvasSize, canvasSize);
        updateBird();
        context.drawImage(bird, birdX, birdY, birdSize * (524 / 374), birdSize);
        context.fillStyle = "#90caf9";
        context.fillRect(pipeX, 0, pipeWidth, topPipeBottomY);
        context.fillRect(pipeX, topPipeBottomY + pipeGap, pipeWidth, canvasSize);
        updatePipe();
        context.fillStyle = "black";
        context.fillText(score++, 9, 25);
        bestScore = bestScore < score ? score : bestScore;
        context.fillText(`High score: ${bestScore}`, 9, 50);
        // Game over
        gameOver() && reset();
      }, interval);
    });

    try {
      bestScore = JSON.parse(localStorage.getItem(FLAPPY_HIGHSCORES)) || [0];
    } catch (err) {
      bestScore = [0];
    }

    function gameOver() {
      const touchingPipe =
        (birdY < topPipeBottomY || birdY > topPipeBottomY + pipeGap) &&
        pipeX < birdSize * (524 / 374);
      const outOfScreen = birdY > canvasSize;
      return touchingPipe || outOfScreen;
    }
    function updateBird() {
      birdY = birdY - birdDy;
      birdDy = birdDy - 0.5;
    }
    function updatePipe() {
      pipeX = pipeX - 8;
      pipeX < -pipeWidth && generatePipe();
    }
    function generatePipe() {
      pipeX = canvasSize;
      topPipeBottomY = pipeGap * Math.random();
    }
    function reset() {
      birdDy = 0;
      birdY = 200;
      score = 0;
      pipeX = canvasSize;
    }
    function handleClick() {
      birdDy = 9;
      bird.src = "/assets/duck-up.svg";
      setTimeout(() => {bird.src = "/assets/duck.svg"}, 200);
    }
    function keydown(event) {
        if(event.keyCode == 32){
            birdDy = 9;
            bird.src = "/assets/duck-up.svg";
            setTimeout(() => {bird.src = "/assets/duck.svg"}, 200);
        }
    }

    // $: bestScore = Math.max(...bestScore);

    $: try {
      localStorage.setItem(FLAPPY_HIGHSCORES, JSON.stringify(bestScore));
    } catch (err) {
      noop
    }
  </script>

 <svelte:window on:keypress={keydown}/>
  
  <style>
    canvas {
      border-radius: 5px;
    }
  </style>
  
  <canvas id="game" width="700" height="700" on:keydown={keydown} on:click={handleClick}/>