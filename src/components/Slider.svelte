<script>
    import { shuffle } from 'lodash-es'
    import { flip } from 'svelte/animate'

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
        }
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
    }
</script>

<svelte:window on:keydown={moveOnKeyDown}/>

<section>
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
        <p>Solved!</p>
        <button on:click={playAgain}>Play again</button>
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
        border: 1px solid rgb(85, 85, 85);
        border-radius: 10px;
        background: rgb(68, 68, 68);
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