<script>
    import {SLIDER_HIGHSCORES} from './snakeconstants';
    import { mdiReplay } from '@mdi/js';
    import { shuffle } from 'lodash-es'
    import { flip } from 'svelte/animate'
    import { Button, Icon } from 'svelte-materialify';

    let sliderScore = 0;
    let bestScore = 0;

    try {
		bestScore = JSON.parse(localStorage.getItem(SLIDER_HIGHSCORES)) || [0];
	} catch (err) {
		bestScore = [0];
	}

    let solution = '123456780'
    let tiles = getShuffledTiles()
    let shouldAnimate = true
    let possibleMoves = [
        [1, 3],
        [0, 2, 4],
        [1, 5],
        [0, 4, 6],
        [1, 3, 5, 7],
        [2, 4, 8],
        [3, 7],
        [4, 6, 8],
        [5, 7]
    ]

    $: solved = tiles.join('') === solution

    function getShuffledTiles () {
        let tiles = shuffle(solution.split('').map(Number))
        while (numberOfInversions(tiles) % 2) {
            tiles = shuffle(solution.split('').map(Number))
        }
        return tiles
    }

    function numberOfInversions (tiles) {
        let inverions = 0

        for (let i = 0; i < tiles.length - 1; i++) {
            if (!tiles[i])
                continue
            for (let j = i + 1; j < tiles.length; j++) {
                if (tiles[j] && tiles[i] > tiles[j])
                    inverions++
            }
        }
        return inverions
    }

    function move (tile, tileCell) {
        if (solved) return
        
        shouldAnimate = true
        let emptyCell = tiles.findIndex(tile => !tile)

        if (possibleMoves[emptyCell].includes(tileCell)) {
            tiles[emptyCell] = tile
            tiles[tileCell] = 0
            tiles = [...tiles]
            sliderScore = sliderScore + 1;

            if (tiles.join('') === solution) {
                bestScore = (bestScore === 0 || bestScore > sliderScore) ? sliderScore : bestScore
            }

            return sliderScore;
        }
        console.log(sliderScore);
    }

    function moveOnKeyDown ({ key }) {
        if (!['ArrowRight', 'ArrowLeft', 'ArrowUp', 'ArrowDown'].includes(key))
            return

        let emptyCell = tiles.findIndex(tile => !tile)
        let nextMove = {
            'ArrowRight': emptyCell - 1,
            'ArrowLeft': emptyCell + 1,
            'ArrowUp': emptyCell + 3,
            'ArrowDown': emptyCell - 3
        }
        let tileCell = nextMove[key]

        move(tiles[tileCell], tileCell)
    }

    function playAgain () {
        shouldAnimate = false
        tiles = getShuffledTiles()

        console.log(bestScore, sliderScore)
        bestScore = (bestScore[0] === 0 || bestScore > sliderScore) ? sliderScore : bestScore;

        sliderScore=0;
    }

    $: try {
      localStorage.setItem(SLIDER_HIGHSCORES, JSON.stringify(bestScore));
    } catch (err) {
      noop
    }

</script>

<svelte:window on:keydown={moveOnKeyDown}/>

<section>
    <div style="display: flex; justify-content: space-between; width: 100%;">
         <p><b>Moves: {sliderScore}</b></p>
         <p><b>Best score: {bestScore}</b></p>
    </div>
   
    <div class="puzzle">
        {#each tiles as tile, index (tile)}
            <div class="tile"
                 class:empty="{!tile}"
                 on:click="{() => move(tile, index)}"
                 animate:flip="{{duration: shouldAnimate ? 150 : 0}}"
            >{tile}</div>
        {/each}
    </div>

    {#if solved}
        <Button on:click={playAgain} style="margin: 25px; background-color: #d4b54e;">
            <Icon path={mdiReplay} style="margin-right: 5px;" />
            Play again</Button>
    {/if}
</section>

<style>
    section {
        display: flex;
        align-items: center;
        flex-direction: column;
    }
    .puzzle {
        display: grid;
        grid-gap: 5px;
        grid-template-columns: repeat(3, 1fr);
        grid-template-rows: repeat(3, 1fr);
        width: 450px;
        height: 450px;
        padding: 5px;
        border-radius: 10px;
        background: #f3d16175;
    }

    .tile {
        line-height: 135px;
        border-radius: 5px;
        background: #f3d161;
        font-size: 65px;
        text-align: center;
        cursor: pointer;
    }

    .empty {
        opacity: 0;
        pointer-events: none;
    }
</style>