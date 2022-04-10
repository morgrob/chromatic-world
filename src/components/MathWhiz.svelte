<svelte:options accessors/>

<script>
  	import {Button, TextField, ProgressLinear } from 'svelte-materialify';
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

    console.log(seconds)
    
    let mode = 'unstarted';

    let score = 0;

    let input;

    var animation;

    let inputField;

    const numberArray = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12];

    var num1 = numberArray[Math.floor(Math.random()*numberArray.length)];
    var num2 = numberArray[Math.floor(Math.random()*numberArray.length)];

    var answer = num1 * num2;

    

    function checkAnswer() {
      if (parseInt(input) === answer) {
        score = score + 1;
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

    function keypress(event) {
      if(event.keyCode == 13){
            checkAnswer()
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
    <p style="font-weight: bold; padding: 10px; margin-bottom: 5%;">Score: {score}</p>
    <p style="font-weight: bold; padding: 10px; margin-bottom: 5%;">{minutes}:{seconds}</p>
  </div>
  <h1 id="question" class="{animation}" style="text-align: center; margin-bottom: 10%; font-weight: light;">{num1} × {num2}</h1>
  <TextField outlined type="number"  class="blue-text text-lighten-2" bind:value={input}>Your Answer</TextField>

{#if minutes >= 0 || seconds >= 0}
  <Button block class="green lighten-2" on:click={checkAnswer} on:keypress={keypress}>submit</Button>
{:else if minutes == 0 && seconds == 0}
  <Button disabled block class="green lighten-2" on:click={checkAnswer} on:keypress={keypress}>submit</Button>
{/if}
<!-- {/if} -->

<style>

  progress[value]::-webkit-progress-value {
    height: 10px;
    background-color: #64b5f6;
    border-radius: 100em;
  }
  progress[value]::-webkit-progress-bar {
    height: 10px;
    background-color: #64b4f63c;
    border-radius: 100em;
  }

</style>