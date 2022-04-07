<script>
    import { onMount, createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();
    export let ref;
    export let condition;
    export let ships;
    export let selectedShip;
    export let orientation;
    export let hasOverlap;
    export let hideShips = false;
    export let guesses;
    export let activePlayer;
    let currentPos;
    let ids = [];
    onMount(() => createIDs());
    $: allPos = () => {
        if (selectedShip) {
            return ships
                .map((s) => (s.type !== selectedShip.type ? s.pos : []))
                .flat();
        } else {
            return ships.map((s) => s.pos).flat();
        }
    }
    $: allHits = () => ships.map(s => s.hits).flat();
    $: if(orientation && selectedShip && currentPos) updateShipPos();
    function createIDs() {
        for (let y = 0; y < 10; y++) {
            for (let x = 0; x < 10; x++) {
                ids = [...ids, `${x}${y}`];
            }
        }
    }
    function handleMouseLeave() {
        if (selectedShip) selectedShip.pos = [];
        currentPos = null;
    }
    function handleClick() {
        if (condition === "placement" && selectedShip) {
            if (saveShipPos(selectedShip)) selectedShip = null;
        } else if (condition === "placement" && !selectedShip) {
            // select already placed ship
            ships.forEach((s) => {
                if (s.pos.includes(currentPos)){
                    let sel = {...s}
                    // clear ship positions to update select button condition
                    ships[ships.findIndex(s => s.type === sel.type)].pos = [];
                    selectedShip = sel;
                }
            });
        } else if (activePlayer == "player"){
            let hit = false;
            ships.forEach((s, i) => {
                if (s.pos.includes(currentPos) &&
                    !guesses.hits.includes(currentPos)) {
                    ships[i] = {...s, hits:[...s.hits, currentPos]};
                    hit = true;
                    guesses = {...guesses, hits: [...guesses.hits, currentPos]};
                    dispatch('turn', {guesses: guesses, activePlayer: 'opponent'})
                }
            })
            if (!hit && !guesses.misses.includes(currentPos) &&
                !guesses.hits.includes(currentPos)) {
                guesses = {...guesses, misses: [...guesses.misses, currentPos]};
                dispatch('turn', {guesses: guesses, activePlayer: 'opponent'})
            }
        }
    }
    function updateShipPos() {
        if (selectedShip) {
            let parsedCurrentPos = currentPos.split("").map((c) => parseInt(c));
            let x = parsedCurrentPos[0];
            let y = parsedCurrentPos[1];
            let pos = [];
            const constrain = (pos, size) =>
                pos > 10 - size ? 10 - size : pos;
            if (orientation === "horizontal") {
                x = constrain(parsedCurrentPos[0], selectedShip.size);
                for (let i = x; i < x + selectedShip.size; i++) {
                    pos.push(`${i}${y}`);
                }
            } else {
                y = constrain(parsedCurrentPos[1], selectedShip.size);
                for (let j = y; j < y + selectedShip.size; j++) {
                    pos.push(`${x}${j}`);
                }
            }
            selectedShip = { ...selectedShip, pos: pos };
        }
    }
    function saveShipPos() {
        const hasNoOverlap = () =>
            selectedShip.pos.every((e) => !allPos().includes(e));
        if (hasNoOverlap()) {
            hasOverlap = false;
            let index = ships.findIndex((e) => e.type === selectedShip.type);
            ships[index] = selectedShip;
            return true;
        }
        hasOverlap = true;
        return false;
    }
    export function placeRandom() {
        const dirs = ["horizontal", "vertical"];
        const randDir = () => dirs[Math.floor(Math.random() * 2)];
        const randPos = () => {
            let randX = Math.floor(Math.random() * 10);
            let randY = Math.floor(Math.random() * 10);
            return `${randX}${randY}`;
        }
        ships.forEach(ship => {
            currentPos = randPos();
            orientation = randDir();
            selectedShip = ship;
            updateShipPos()
            while (!saveShipPos()) {
                currentPos = randPos();
                orientation = randDir();
                updateShipPos()
            }
            selectedShip = null;
            currentPos = null;
        });
    }
</script>

<style>
    p {
        cursor: default;
        padding: 0;
        margin: 0;
        font-size: 14px;
    }
    .large {
        font-size: 34px;
    }
    .grid-container {
        display: grid;
        height: 100%;
        width: 100%;
        grid-template-columns: repeat(10, 1fr);
        grid-template-rows: repeat(10, 1fr);
        grid-gap: 2px;
        /* box-shadow: 10px 10px 30px rgb(17, 17, 17); */
    }
    .disable {
        pointer-events: none;
    }
    .grid-square {
        display: flex;
        justify-content: center;
        align-items: center;
        background-color:rgb(46, 46, 46);
    }
    .grid-square:hover {
        border: 2px solid white;
    }
    .ship {
        cursor: pointer;
        background-color: #64b5f6;
    }
    .selectedShip {
        border: none;
        background-color:#90caf9;
        position: relative;
        top: -8px;
        left: -8px;
    }
    .overlap {
        background-color: #f44336;
    }
    .large {
        font-size: 30px;
    }
</style>

<div
    {ref}
    class="grid-container"
    on:mouseleave={() => handleMouseLeave()}>
    {#each ids as id}
        <div
            {id}
            class="grid-square"
            on:mouseenter={() => currentPos = id}
            on:click={() => handleClick(id)}
            class:ship={hideShips ? allHits().includes(id) : allPos().includes(id)}
            class:selectedShip={selectedShip && selectedShip.pos.includes(id)}
            class:disable={ref == "grid-2"}
            class:overlap={allHits().includes(id) || allPos().includes(id) && selectedShip && selectedShip.pos.includes(id)}>
            <p class:large={ref == "grid-1"}>{@html guesses.misses.includes(id) ? "&#10005;" : ""}</p>
        </div>
    {/each}
</div>