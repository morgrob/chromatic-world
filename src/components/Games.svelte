<script>

</script>

<!-- <img class="icon" src="assets/game-icons/tetris.svg" alt="tetris icon"> -->

<div class="container">
    <div class="row row-1">
        <a href="/"><div class="game tetris">TETRIS</div></a>
        <a href="/"><div class="game wildwest">WILD WEST</div></a>
    </div>
    <div class="row row-2">
        <a href="/"><div class="game mastermind">MASTERMIND</div></a>
        <a href="/"><div class="game snake">HUNGRY SNAKE</div></a>
        <a href="/"><div class="game nonograms">NONOGRAMS</div></a>
    </div>
    <div class="row row-3">
        <a href="/"><div class="game twenty">2048</div></a>
        <a href="/"><div class="game breakout">BREAKOUT</div></a>
    </div>
</div>

<style>
    .container {
        display: flex;
        flex-direction: column;
        height: 100vh;
        justify-content: center;
    }
    .row {
        display: flex;
        justify-content: center;
    }
    .game {
        height: 225px;
        width: 225px;
        background-color: gray;
        margin: 0 10px;
        border-radius: 100em;
        display: flex;
        flex-direction: column;
        justify-content: center;
        text-align: center;
        font-weight: bold;
        font-size: 26px;
    }
    a {
        transition: 0.2s ease-in-out;
    }
    .tetris {
        background-color: #FF4E4E;
        color: #fc9f9f;
    }
    /* .icon {
        width: 125px;
        margin: 0 auto;
    } */
    .wildwest {
        background-color: #FF9452;
        color: #fdc29c;
    }
    .mastermind {
        background-color: #B871FF;
        color: #debeff;
    }
    .snake {
        background-color: #FF90D4;
        color: #fdccea;
    }
    .nonograms {
        background-color: #FCDB6C;
        color: #fff4d1;
    }
    .twenty {
        background-color: #91B7FF;
        color: #c8dbff;
    }
    .breakout {
        background-color: #A1F08E;
        color: #d7fdce;
    }
    a:hover {
        text-decoration: none;
        transform: scale(1.1);
    }
</style>