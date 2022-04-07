<script>
    import { fade } from 'svelte/transition';
    import { createEventDispatcher } from 'svelte';
    import { Button } from 'svelte-materialify';
    const dispatch = createEventDispatcher();
    export let ref;
    export let condition;
    export let canStartGame;
    let newGame = false;
    function handleNewGame() {
        dispatch('newGame');
        newGame = false;
    }
</script>

<style>
    .alert-container {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        cursor: pointer;
    }
    /* .inactive {
        transition: color 1s, background-color 1s;
    }
    .active {
        border: 2px solid #333;
        box-shadow: 10px 10px 30px #333;
    } */
</style>

{#if condition == "placement"}
    {#if canStartGame}
        <div transition:fade {ref} class="alert-container active" on:click={() =>
            dispatch("start")}>
            <Button block class="blue lighten-2">Start Game</Button>
        </div>
    {:else}
        <div transition:fade {ref} class="alert-container inactive" on:click={() =>
            dispatch("start")}>
            <Button block disabled>Start Game</Button>
        </div>
    {/if}
{:else}
    {#if newGame}
        <div transition:fade {ref} class="alert-container active" on:click={() => newGame = true} on:click={() => handleNewGame()}>
            <Button block class="blue lighten-2">New Game</Button>
        </div>
    {:else}
        <div transition:fade {ref} class="alert-container inactive" on:click={() => newGame = true} on:click={() => handleNewGame()}>
            <Button block class="blue lighten-2">New Game</Button>
        </div>
    {/if}
{/if}