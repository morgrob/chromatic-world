<script>
    import { mdiPhoneRotatePortrait, mdiPhoneRotateLandscape } from '@mdi/js';
    import { Button, Icon } from 'svelte-materialify';
    export let orientation;
    function changeOrientation(event) {
        if (event && event.keyCode === 32) {
            orientation = orientation === "horizontal" ? "vertical" : "horizontal"
            return;
        } else if (!event) {
            orientation = orientation === "horizontal" ?
                "vertical" : "horizontal";
        }
    }
</script>

<style>
    /* h2 {
        cursor: pointer;
        margin-left: 20px;
    } */
</style>

<svelte:window on:keydown={(e) => changeOrientation(e)} />
<Button block on:click={() => changeOrientation()} style="margin: 10px auto;">
    {#if orientation === "horizontal"}
        <Icon path={mdiPhoneRotatePortrait} style="margin-right: 10px;"/> Rotate Ship
    {:else}
        <Icon path={mdiPhoneRotateLandscape} style="margin-right: 10px;"/> Rotate Ship
    {/if}
</Button>