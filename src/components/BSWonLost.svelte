<script>
    import { fade } from 'svelte/transition';
    export let ref;
    export let winner;
</script>

<style>
    #message-container {
        background-color: rgba(22, 22, 22, 0.624);
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .message {
        font-size: 35pt;
    }
</style>

<div {ref} transition:fade id="message-container">
    {#if winner() == 'player'}
        <div class="message">
            <h1 style="font-size: 35pt;">You won! 🎉</h1>
        </div>
    {:else}
        <div class="message">
            <h1 style="font-size: 35pt;">You lost... 🙁</h1>
        </div>
    {/if}
</div>