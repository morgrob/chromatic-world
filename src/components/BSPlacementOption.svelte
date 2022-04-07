<script>
    import { createEventDispatcher } from "svelte";
    import { Button } from 'svelte-materialify';
    const dispatch = createEventDispatcher();
    export let condition;
    function handleClick(type) {
        if (condition == "placement") dispatch(type)
    }
</script>

<style>
    /* div {
        padding: 0;
        margin: 0;
        margin-left: 20px;
        cursor: pointer;
        font-size: 30px;
        display: block;
    } */
</style>

<div id="options-container" style="padding: 5px;">
    <Button block on:click={() => handleClick('random')} style="margin: 5px 0px;">Place Ships for me</Button>
    <Button block on:click={() => handleClick("clear")} style="margin: 5px 0px;">clear board</Button>
</div>