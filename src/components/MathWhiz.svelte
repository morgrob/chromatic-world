<svelte:options accessors/>

<script>
  	import {Button, TextField, Icon } from 'svelte-materialify';
    import { MATH_HIGHSCORES } from './snakeconstants';
    import { mdiReplay } from '@mdi/js';

    import { tweened } from 'svelte/motion';
    let original = 3 * 60; // TYPE NUMBER OF SECONDS HERE
    let timer = tweened(original)

    setInterval(() => {
      if ($timer > 0) $timer--;
    }, 1000);

    function zeroPadded(number) {
      return number >= 10 ? number.toString() : `0${number}`;
    }

    $: minutes = Math.floor($timer / 60);
    $: seconds = zeroPadded(Math.floor($timer - minutes * 60))
    
    let score = 0;
    let bestScore;

    let input;

    var animation;

    const numberArray = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12];

    var num1 = numberArray[Math.floor(Math.random()*numberArray.length)];
    var num2 = numberArray[Math.floor(Math.random()*numberArray.length)];

    var answer = num1 * num2;



    function checkAnswer() {
      if (minutes > 0 || seconds > 0) {
        if (parseInt(input) === answer) {
          score = score + 1;
          bestScore = bestScore < score ? score : bestScore;
          num1 = numberArray[Math.floor(Math.random()*numberArray.length)];
          num2 = numberArray[Math.floor(Math.random()*numberArray.length)];
          answer = num1 * num2;
          input = '';
        } else {
          animation = "shake-horizontal shake-constant";
          setTimeout(() => {animation = "null"}, 500);
          input = '';
        }
      }
      
    }
    function newGame() {
      score = 0;
      num1 = numberArray[Math.floor(Math.random()*numberArray.length)];
      num2 = numberArray[Math.floor(Math.random()*numberArray.length)];
      original = 3*60;
      timer = tweened(original);
      setInterval();
      minutes = Math.floor($timer / 60);
      seconds = zeroPadded(Math.floor($timer - minutes * 60))
    }

    try {
      bestScore = JSON.parse(localStorage.getItem(MATH_HIGHSCORES)) || [0];
    } catch (err) {
      bestScore = [0];
    }

    $: try {
      localStorage.setItem(MATH_HIGHSCORES, JSON.stringify(bestScore));
    } catch (err) {
      noop
    }

    function keypress(event) {
      if (minutes > 0 || seconds > 0) {
        if(event.keyCode == 13){
            checkAnswer()
        }
      }
    }

</script>

<svelte:window on:keypress={keypress}/>

<!-- {#if mode === 'unstarted'}

  <img src="/assets/mathwhiz.png" alt="Math Illustration" style="width: 50%; margin: 5% 25%;">
  <p style="text-align: center;" class="grey-text text-darken-1"><i>Answer as many questions as you can in 3 minutes.</i></p>
  <Button block on:click={mode='start'} class="deep-purple accent-1">Start Game</Button>
  

{:else if mode === 'start'} -->


  <progress value={$timer/original} style="width: 100%;"></progress> 
  <div class="top-text" style="display: flex; justify-content: space-between;">
    <div>
        <p style="font-weight: bold; padding-top: 0px; padding-left: 0px; margin-bottom: 5%;">Score: {score}</p>
        <p style="font-weight: bold; padding-left: 0px; margin-bottom: 5%;">High Score: {bestScore}</p>
    </div>
    <p style="font-weight: bold; padding: 0px; margin-bottom: 5%;">{minutes}:{seconds}</p>
  </div>
  <h1 id="question" class="{animation}" style="text-align: center; margin-bottom: 10%; font-weight: light;">{num1} × {num2}</h1>
  <TextField outlined type="number" autofocus="autofocus" onfocus="this.select()" class="blue-text text-lighten-2" bind:value={input}>Your Answer</TextField>

{#if minutes > 0 || seconds > 0}
  <Button block class="red" on:click={checkAnswer} on:keypress={keypress}>submit</Button>
{:else if minutes == 0 && seconds == 0}
  <Button block disabled>submit</Button>
  <Button block on:click={newGame}>
    <Icon path={mdiReplay} style="margin-right: 5px;" />
    Play again</Button>
  <!-- <Button block class="blue lighten-2" on:click={newGame} on:keypress={keypress}>
    <Icon path={mdiReplay} style="margin-right: 5px;" />
    play again</Button> -->
{/if}
<!-- {/if} -->

<style>

  progress[value]::-webkit-progress-value {
    height: 10px;
    background-color: #f44336;
    border-radius: 100em;
  }
  progress[value]::-webkit-progress-bar {
    height: 10px;
    background-color: #f443363f;
    border-radius: 100em;
  }

</style>