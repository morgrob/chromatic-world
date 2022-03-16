<script>

    import { Button } from 'svelte-materialify';

</script>

<div class="headline">
    <div class="headline-img">
        <img src="assets/chromatic-header.png" alt="chromatic.world expanded logo">
        <h1 id="world">.world</h1>
    </div>
    <h1>Let the games begin.</h1>
    <Button rounded class="deep-purple accent-1 white-text" size="x-large" style="width: 200px; font-size: 14pt;">PLAY NOW</Button>
</div>

<style>
    .headline-img {
        display: flex;
    }
    #world {
        font-size: 62pt;
        transform: translateY(13px);
    }
    img {
        width: 25em;
        height: auto;
    }
    h1 {
        font-size: 2.5em;

    }
    /* .button {
        width: 190px;
        padding: 10px 0;
        border-radius: 100em;
        color: white;
        background-color: #B871FF;
        border: none;
        font-size: 1.5em;
        font-weight: bold;
        text-align: center;
    }
    .button {
        display: inline-block;
        border-radius: 10rem;
        color: #fff;
        text-transform: uppercase;
        transition: all 0.3s;
        position: relative;
        overflow: hidden;
        z-index: 1;
    }
    .button:after {
        content: "";
        position: absolute;
        bottom: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: #B871FF;
        border-radius: 10rem;
        z-index: -2;
    }
    .button:before {
        content: "";
        position: absolute;
        bottom: 0;
        left: 0;
        width: 0%;
        height: 100%;
        background-color: #FF90D4;
        transition: all 0.3s;
        border-radius: 10rem;
        z-index: -1;
    }
    .button:hover {
        text-decoration: none;
    }
    .button:hover:before {
        width: 100%;
    } */
    .headline {
        height: 100vh;
        display: flex;
        flex-direction: column;
        justify-content: center;
        padding-left: 50px;
    }
</style>