<script>
    // import { showingLoginForm } from "../store"

    // export function logIn() {
    //     showingLoginForm.update(value => {
    //         return !value
    //     })
    // }

    // export let buttonColor = '#6e6e6e';

    // showingLoginForm.subscribe(value => {
    //     buttonColor = value ? '#ff0000' : '#6e6e6e';
    // })

    import { Button } from 'svelte-materialify';

    let active = false;
</script>

<!-- <div class="nav-buttons" style="--theme-color: {buttonColor}">
    <a class="button" id="login" on:click={logIn}>Log in</a> -->

<header>
    <img id="logo" src="assets/favicon.png" alt="chromatic.world logo">
    <div class="nav-buttons">
        <Button class="button" id="login" on:click={() => {active = true;}}>Log in</Button>
    </div>
</header>

<style>
    header {
        background-color: #161616;
        height: 60px;
        width: 100vw;
        display: flex;
        position: fixed;
        justify-content: space-between;
        padding: 0;
        margin: 0;
        box-shadow: rgba(0, 0, 0, 0.5) 0px 2px 8px 0px;   
        z-index: 100; 
    }
    #logo {
        height: 62px;
        padding: 12.5px;
    }
    .nav-buttons {
        padding: 12.5px;
        display: flex;
        justify-content: space-between;
    }
    /* .button {
        padding: 6px 0;
        width: 80px;
        border-radius: 100em;
        border: none;
        color: white;
        font-weight: 500;
        text-align: center;
        transition: 0.2s ease-in-out;
    }
    .button:hover {
        text-decoration: none;
    }
    #login {
        background-color: var(--theme-color);
        background-color: #6e6e6e;
    }
    #login:hover {
        background-color: #616161;
    }
    #signup {
        background-color: #B871FF;
    }
    #signup:hover {
        background-color: #a244ff;
    } */
</style>