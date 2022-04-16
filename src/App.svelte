<script>
	// Import the functions you need from the SDKs you need
	import { initializeApp } from "firebase/app";
	import { WORLDLE_BANK } from "./components/snakeconstants";
	// import { getAnalytics } from "firebase/analytics";
	import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword, signOut, updateProfile } from "firebase/auth";

	import { onMount, createEventDispatcher } from 'svelte';
	// var randomWords = require('random-words')
	import WordGrid from './components/WordGrid.svelte';
	import Keyboard from './components/Keyboard.svelte';
	import Snake from "./components/Snake.svelte";
	import Breakout from "./components/Breakout.svelte";
	import Slider from "./components/Slider.svelte";
	import FlappyBird from "./components/FlappyBird.svelte";
	import MathWhiz from "./components/MathWhiz.svelte";
	import Leaderboard from "./components/Leaderboard.svelte";

	import BSGrid from "./components/BSGrid.svelte";
    import BSShipSelect from "./components/BSShipSelect.svelte";
    import BSPlacementOption from "./components/BSPlacementOption.svelte";
    import BSOrientationBtn from "./components/BSOrientationBtn.svelte";
    import BSStartNew from "./components/BSStartNew.svelte";
    import BSWonLost from "./components/BSWonLost.svelte";

	// TODO: Add SDKs for Firebase products that you want to use
	// https://firebase.google.com/docs/web/setup#available-libraries

	// Your web app's Firebase configuration
	// For Firebase JS SDK v7.20.0 and later, measurementId is optional
	const firebaseApp = initializeApp({
		apiKey: "AIzaSyBPymhx_TZRKujqsipusuORVVBUgdnjhZ0",
		authDomain: "chromatic-world.firebaseapp.com",
		projectId: "chromatic-world",
		storageBucket: "chromatic-world.appspot.com",
		messagingSenderId: "685254612981",
		appId: "1:685254612981:web:58ffa2599136c5277932bc",
		measurementId: "G-MNXNRJT6GQ"
	});

	import Headline from "./components/Headline.svelte";

	import { Menu, List, ListItem, TextField, Icon, Button, Overlay, MaterialApp, Card } from 'svelte-materialify';
	import { mdiInvertColors, mdiAccountCircle, mdiLock, mdiEyeOff, mdiEye, mdiAt, mdiLogout, mdiClose, mdiReplay, mdiCalculatorVariant } from '@mdi/js';

	export let theme = 'dark';

    let active = false;

	let show = false;

	function toggleTheme() {
		if (theme === 'dark') theme = 'light';
		else theme = 'dark';
    }

	const emailRules = [
		(v) => !!v || 'Required',
		(v) => v.length <= 25 || 'Max 25 characters',
		(v) => {
			const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
			return pattern.test(v) || 'Invalid email.';
		},
	];

	const createPassRules = [
		(v) => !!v || 'Required',
    	(v) => v.length >= 8 || 'Minimum 8 characters.',
    	(v) => {
			const pattern = /^(?=.*\d)(?=.*[a-z])(?=.*[A-Z])[0-9a-zA-Z]{8,}$/
      		return pattern.test(v) || 'Password must be at least 8 characters and contain at least one uppercase, one lowercase, and one number';
    	},
	];

	const logPassRules = [
		(v) => !!v || 'Required',
	];

	const createUserRules = [
		(v) => !!v || 'Required',
    	(v) => v.length <= 25 || 'Max 25 characters.',
    	(v) => {
			const pattern = /^[a-zA-Z\-0-9_]+$/
      		return pattern.test(v) || 'Invalid username.';
    	},
	];

	let email = ''
	let password = ''
	let username = ''

	let mode = 'signIn'

	const auth = getAuth(firebaseApp);

	auth.onAuthStateChanged((user) => {
		if (user != null) {
			if (user.displayName != null) {
				username = user.displayName
			}
			mode = 'loggedIn'
		} else {
			email = ''
			password = ''
			username = ''
			mode = 'signIn'
		}
	})

	const loginEmailPassword = async () => {
		const loginEmail = email;
		const loginPassword = password;

		const satisfiesEmailRules = emailRules.map(rule => rule(email)).every(result => result === true)
		const satisfiesPassRules = logPassRules.map(rule => rule(password)).every(result => result === true)
		
		if (!satisfiesEmailRules) {
			hasEmailError = true
		}
		if (!satisfiesPassRules) {
			hasPasswordError = true
		}

		if (satisfiesEmailRules === true && satisfiesPassRules === true) {
			try {
				await signInWithEmailAndPassword(auth, loginEmail, loginPassword);
				active = false;
			}
			catch(error) {
				console.log(error);
			}
		}
	}

	// let createFieldClass = "pink-text text-accent-1";
	var hasEmailError = false
	var hasUsernameError = false
	var hasPasswordError = false

	const createEmailPassword = async () => {
		const loginEmail = email;
		const loginPassword = password;

		const satisfiesEmailRules = emailRules.map(rule => rule(email)).every(result => result === true)
		const satisfiesPassRules = createPassRules.map(rule => rule(password)).every(result => result === true)
		const satisfiesUsernameRules = createUserRules.map(rule => rule(username)).every(result => result === true)

		if (!satisfiesEmailRules) {
			hasEmailError = true
		}

		if (!satisfiesUsernameRules) {
			hasUsernameError = true
		}

		if (!satisfiesPassRules) {
			hasPasswordError = true
		}

		if (satisfiesEmailRules === true && satisfiesUsernameRules === true && satisfiesPassRules === true ) {
			try {
				const userCredential = await createUserWithEmailAndPassword(auth, loginEmail, loginPassword);

				updateProfile(userCredential.user, { displayName: username })
					.then(() => {
						active = false;
					})
			} catch(error) {
				console.log(error);
			}
		}
	}

	const logoutEmailPassword = async () => {
		const loginEmail = email;
		const loginPassword = password;

		try {
			const userCredential = await signOut(auth, loginEmail, loginPassword);
			mode = 'signIn';
		}
		catch(error) {
			console.log(error);
		}
	}

	let overlayMode = null;

	const setOverlayMode = (mode) => {
		active = mode !== null;
		overlayMode = mode;
	}

	/////////////////////////////////////////////////////// START SWORDLE CODE HERE ///////////////////////////////////////////////////////

	let word = ""
	let state = 0 // number of swordle guesses so far
	let tries = 7
	let results = [] // 
	let guesses = makeGuesses();
	let guessColors = makeGuessColors();

	window["keypress"] = []
	window["keycolor"] = []
	let message = ""

	let keycolors = {}

	function makeGuessColors() {
		let result = [];

		for (let i = 0; i < 7; i++) {
			let array = Array(6).fill("waiting")
			result.push(array)
		}

		return result
	}

	function makeGuesses() {
		let result = Array(7).fill("")
		return result
	}

	function handleMessage(event){
		if(event.detail.message != undefined){
			message = event.detail.message
			setTimeout(() => {
				message = ""
			}, 1000)
			if (message != "That's not a word!") {
				state--;
			}
		} else{
			let x = event.detail.state
			results.push(x)
			if(x == "correct"){
				state = "win"
			} 
			else if(x == "incorrect"){
				if(state + 1 >= tries){
					state = "lose"
				} else{
					state++
				}
			}
		}
	}
	function handleKeyclick(event){
		console.log(event.detail.key)
		return {key: event.detail.key}
	}
	function getState(i) {
		if (i == state) {
			return "typing"
		} else if (i > state) {
			return "waiting"
		} else {
			return results[i]
		}
	}

	// var randomWord = ['abroad', 'across', 'breeze', 'energy', 'motion']

	onMount(() => {
		if(word == ""){
			word = WORLDLE_BANK[Math.floor(Math.random()*WORLDLE_BANK.length)]
			console.log(word)
			tries = 7
			state = 0
			results = []
		}
	})

	function refreshSwordle() {
		word = WORLDLE_BANK[Math.floor(Math.random()*WORLDLE_BANK.length)]
		tries = 7
		state = 0
		results = []
		guesses = makeGuesses();
		guessColors = makeGuessColors();
		keycolors = {}
	}

	/////////////////////////////////////////////////////// START BATTLESHIP CODE HERE ///////////////////////////////////////////////////////

	let conditions = ["placement", "game"];
    let condition = conditions[0];
    let activePlayer = 'player';
    // prettier-ignore
    let playerShips = [
        { type: "carrier",    size: 5, hits: [], pos: [] },
        { type: "battleship", size: 4, hits: [], pos: [] },
        { type: "cruiser",    size: 3, hits: [], pos: [] },
        { type: "submarine",  size: 3, hits: [], pos: [] },
        { type: "destroyer",  size: 2, hits: [], pos: [] },
    ];
    let opponentShips = [
        { type: "carrier",    size: 5, hits: [], pos: [] },
        { type: "battleship", size: 4, hits: [], pos: [] },
        { type: "cruiser",    size: 3, hits: [], pos: [] },
        { type: "submarine",  size: 3, hits: [], pos: [] },
        { type: "destroyer",  size: 2, hits: [], pos: [] },
    ];
    let playerGuesses = {hits: [], misses: []};
    let opponentGuesses = {hits: [], misses: []};
    let opponentPossibleGuesses = [];
    createGuesses();
    function createGuesses() {
        for (let y = 0; y < 10; y++) {
            for (let x = 0; x < 10; x++) {
                opponentPossibleGuesses = [...opponentPossibleGuesses, `${x}${y}`];
            }
        }
    }
    $: numOfShipsPlaced = playerShips.filter(s => s.pos.length > 1).length;
    let selectedShip = null;
    let orientation = "horizontal";
    let hasOverlap = false;
    let playerGridEl;
    let opponentGridEl;
    let messagesEl;
    function reset() {
        condition = conditions[0]
        playerShips = [
            { type: "carrier",    size: 5, hits: [], pos: [] },
            { type: "battleship", size: 4, hits: [], pos: [] },
            { type: "cruiser",    size: 3, hits: [], pos: [] },
            { type: "submarine",  size: 3, hits: [], pos: [] },
            { type: "destroyer",  size: 2, hits: [], pos: [] },
        ];
        opponentShips = [
            { type: "carrier",    size: 5, hits: [], pos: [] },
            { type: "battleship", size: 4, hits: [], pos: [] },
            { type: "cruiser",    size: 3, hits: [], pos: [] },
            { type: "submarine",  size: 3, hits: [], pos: [] },
            { type: "destroyer",  size: 2, hits: [], pos: [] },
        ];
        playerGuesses = {hits: [], misses: []}
        opponentGuesses = {hits: [], misses: []}
    }
    function clearShips() {
        playerShips = playerShips.map(s => {
            return {...s, pos: []}
        })
    }
    $: canStartGame = numOfShipsPlaced == 5 && condition == "placement" ? true : false;
    function handleStart() {
        if (canStartGame) {
            condition = conditions[1]
            opponentGridEl.placeRandom();
        }
        messagesEl.startGameMsg(canStartGame);
    }
    $: winner = () => {
        if (playerShips.map(s => s.hits).flat().length == 17) {
            return 'opponent'
        } else if (opponentShips.map(s => s.hits).flat().length == 17) {
            return 'player'
        }
    }
    function opponentTurn() {
        let randIndex = Math.floor(Math.random() *
            opponentPossibleGuesses.length);
        let randPos = opponentPossibleGuesses.splice(randIndex, 1)[0];
        let hit = false;
        playerShips.forEach((s, i) => {
            if (s.pos.includes(randPos)) {
                playerShips[i] = {...s, hits:[...s.hits, randPos]};
                hit = true;
            }
        })
        if (!hit ) opponentGuesses = {...opponentGuesses,
            misses:[...opponentGuesses.misses, randPos] };
        activePlayer = "player";
    }
    $: if (activePlayer == 'opponent') setTimeout(() => opponentTurn(), 1000)
    function handleTurn(e) {
        playerGuesses = e.guesses;
        activePlayer = e.activePlayer;
    }

</script>

	<MaterialApp theme="{theme}">

		<div class="container">

			<header>
				<img id="logo" src="assets/favicon.png" alt="chromatic.world logo">
				<div class="nav-buttons">
					{#if mode === 'loggedIn'}
						<Menu hover>
							<div slot="activator" style="margin-top: 8px;" class="grey-text text-darken-2">
								<!-- <Button class="pink accent-1" style="margin-top: 12px;"> -->
									<Icon path={mdiAccountCircle} style="margin-right: 5px;" class="grey-text text-darken-2"/>
									{username}
								<!-- </Button> -->
							</div>

							<List style="padding: 0;">
								<ListItem on:click="{logoutEmailPassword}" style="height: 50px; padding: 10px;">
									<Icon path={mdiLogout}/>
									Log out
								</ListItem>
							</List>
					  	</Menu>
					{:else}
						<Button rounded class="button deep-purple accent-1 white-text" id="login" on:click={() => { setOverlayMode('form') }} style="margin-top: 2px; padding: 18px 20px">Log in</Button>
					{/if}
				</div>
				<div class="footer-items" style="position: fixed; bottom: 20px; right: 20px;">
					<p id="main" style="margin: 10px 25px;">Made with <b>&hearts;</b> by Morgan Roberts &copy; 2022</p>
					<Button fab size="small" on:click="{toggleTheme}">
						<Icon path={mdiInvertColors}/>
					</Button>
				</div>
				
			</header>

			<div class="main-container">

				<Headline/>

				<div class="game-container">
					<div class="row row-1">
						<Button rounded class="red accent-2 game math white-text" on:click={() => { setOverlayMode('math') }} style="height: 200px; width: 200px; margin: 0 8px; font-size: 14pt;">MATH WHIZ</Button>
						<Button rounded class="game flappy white-text" on:click={() => { setOverlayMode('flappy') }} style="height: 200px; width: 200px; margin: 0 8px; background-color: #FF9452; font-size: 14pt;">Flappy Duck</Button>
					</div>
					<div class="row row-2">
						<Button rounded class="game swordle deep-purple accent-1 white-text" on:click={() => { setOverlayMode('swordle') }} style="height: 200px; width: 200px; margin: 0 8px; font-size: 14pt;">swordle</Button>
						<Button rounded class="game snake pink accent-1 white-text" on:click={() => { setOverlayMode('snake') }} style="height: 200px; width: 200px; margin: 0 8px; font-size: 14pt;">HUNGRY SNAKE</Button>
						<Button rounded class="game slider white-text" on:click={() => { setOverlayMode('slider') }} style="height: 200px; width: 200px; margin: 0 8px; background-color: #f3d161; font-size: 14pt;">SLIDER PUZZLE</Button>
					</div>
					<div class="row row-3">
						<Button rounded class="blue lighten-2 game battle white-text" on:click={() => { setOverlayMode('battle') }} style="height: 200px; width: 200px; margin: 0 8px; font-size: 14pt;">BATTLESHIP</Button>
						<Button rounded class="green lighten-2 game breakout white-text" on:click={() => { setOverlayMode('breakout') }} style="height: 200px; width: 200px; margin: 0 8px; font-size: 14pt;">BREAKOUT</Button>
					</div>
				</div>

			</div>

			

			<!-- <footer>
				<p id="main">Made with <b>&hearts;</b> by Morgan Roberts &copy; 2022</p>
				<div class="links">
					<a href="/">About</a>
					<a href="/">Privacy Policy</a>
					<a href="/">Help</a>
				</div>
			</footer> -->

			
			<Overlay opacity={1} {active} color={theme === 'dark' ? 'grey darken-4' : 'grey lighten-5'}>

				{#if overlayMode === 'form'}

					<form id="login-container" on:submit|preventDefault={loginEmailPassword}>
						{#if mode === 'signIn'}
							<h4 id="login-head">Welcome back!</h4>

							{#if hasEmailError || hasPasswordError}
								<p class="red-text" style="margin-bottom: 20px; margin-top: -20px;">Login failed.</p>
							{/if}

							<TextField class="deep-purple-text text-accent-1" rules={emailRules} bind:value={email}>
								<div slot="prepend-outer">
								<Icon path={mdiAt} />
								</div>
								Email
							</TextField>

							<br/>
							
							<TextField type={show ? 'text' : 'password'} class="deep-purple-text text-accent-1" bind:value={password} rules={logPassRules}>
								<div slot="prepend-outer">
								<Icon path={mdiLock} />
								</div>
								Password
								<div slot="append" on:click={() => {show = !show;}}>
									<Icon path={show ? mdiEyeOff : mdiEye} />
								</div>
							</TextField>

							<br/>
							
							<Button on:click={loginEmailPassword} block class="deep-purple accent-1">Log in</Button>
						{:else}
							<h4>Welcome!</h4>
							{#if hasEmailError || hasUsernameError || hasPasswordError}
								<p class="red-text" style="margin-bottom: 20px; margin-top: -20px;">Account creation failed.</p>
							{/if}
							<TextField class="pink-text text-accent-1" rules={emailRules} bind:value={email}>
								<div slot="prepend-outer">
								<Icon path={mdiAt} />
								</div>
								Email
							</TextField>

							<br/>

							<TextField class="pink-text text-accent-1" rules={createUserRules} bind:value={username}>
								<div slot="prepend-outer">
								<Icon path={mdiAccountCircle} />
								</div>
								Username
							</TextField>

							<br/>

							<TextField type={show ? 'text' : 'password'} class="pink-text text-accent-1" rules={createPassRules} bind:value={password}>
								<div slot="prepend-outer">
								<Icon path={mdiLock} />
								</div>
								Password
								<div slot="append" on:click={() => {show = !show;}}>
									<Icon path={show ? mdiEyeOff : mdiEye} />
								</div>
							</TextField>
							<br/>
							<Button on:click={createEmailPassword} block class="pink accent-1">Create Account</Button>
						{/if}
						<Button block on:click={() => { setOverlayMode(null) }}>Cancel</Button>
						{#if mode === 'signIn'}
							<p style="text-align: center;">Don't have an account? <b class="deep-purple-text text-accent-1" on:click={() => (mode = 'signUp')}>Create one.</b></p>
						{:else}
							<p style="text-align: center;">Already have an account? <b class="pink-text text-accent-1" on:click={() => (mode = 'signIn')}>Log in.</b></p>
						{/if}
					</form>

				{:else if overlayMode === "math"}

					<Button text on:click={() => { setOverlayMode(null) }} class="grey-text text-darken-2">
						<Icon path={mdiClose} style="margin-right: 5px;" /> close
					</Button>

					<div style="display: flex;">

						<div id="math">
							<!-- <h3 style="text-align: center; letter-spacing: 8px; margin-bottom: 25px;">MATH WHIZ</h3> -->
							<!-- <h3 style="text-align: center; margin-bottom: 25px;">🧮 MATH WHIZ 🧮</h3> -->
							<h3 style="text-align: center; margin-bottom: 25px;">MATH WHIZ</h3>

							<Card style="padding: 20px; width: 100%; background-color: {theme === 'dark' ? '#151515' : 'grey lighten-5'};">
								<MathWhiz/>
							</Card>
						</div>

					</div>

				{:else if overlayMode === "flappy"}

					<Button text on:click={() => { setOverlayMode(null) }} class="grey-text text-darken-2">
						<Icon path={mdiClose} style="margin-right: 5px;" /> close
					</Button>

					<!-- <h3 style="text-align: center; letter-spacing: 8px; margin-bottom: 25px; color: #FF9452;">FLAPPY DUCK</h3> -->
					<!-- <h3 style="text-align: center; margin-bottom: 25px;">🪶 FLAPPY DUCK 🪶</h3> -->
					<h3 style="text-align: center; margin-bottom: 25px;">FLAPPY DUCK</h3>

					<div class="flappy-container">
						<FlappyBird/>
					</div>
				
				{:else if overlayMode === "swordle"}

						<Button text on:click={() => { setOverlayMode(null) }} class="grey-text text-darken-2" style="margin-top: 50px;">
							<Icon path={mdiClose} style="margin-right: 5px;" /> close
						</Button>

						<!-- <h3 style="text-align: center; letter-spacing: 8px; margin-bottom: 25px;" class="deep-purple-text text-accent-1">WORDLE6</h3> -->
						<!-- <h3 style="text-align: center; margin-bottom: 25px;">💡 SWORDLE 💡</h3> -->
						<h3 style="text-align: center; margin-bottom: 25px;">SWORDLE</h3>

					
						<div class="message" style="display: flex; justify-content: space-between;">
							{#if message != ""}
								<strong>{message}</strong>
							{/if}

							{#if state == "win"}
								<strong>You won! 🎉</strong>
								<Button class="deep-purple accent-1" on:click={refreshSwordle}>
									<Icon path={mdiReplay} style="margin-right: 5px;" />
									Play Again
								</Button>
							{/if}

							{#if state == "lose"}
								<strong>You lost! 🥴</strong>
								<p>The word was {word}.</p>
								<Button class="deep-purple darken-3" on:click={state = 0}>
									<Icon path={mdiReplay} style="margin-right: 5px;" />
									Play Again
								</Button>
							{/if}
						</div>
						<br>
						<table>
							{#each {length: tries} as _, i}
								<WordGrid state={getState(i, state)} correct={word} word={guesses[i]} colors={guessColors[i]} on:message={handleMessage}/>
							{/each}
						</table>

						<Keyboard on:keyclick={handleKeyclick} keycolors={keycolors}/>

				{:else if overlayMode === "snake"}

					<Button text on:click={() => { setOverlayMode(null) }} class="grey-text text-darken-2">
						<Icon path={mdiClose} style="margin-right: 5px;" /> close
					</Button>

					<!-- <h3 style="text-align: center; letter-spacing: 8px; margin-bottom: 25px;" class="pink-text text-accent-1">HUNGRY SNAKE</h3> -->
					<!-- <h3 style="text-align: center; margin-bottom: 25px;">🧀  HUNGRY SNAKE  🐍</h3> -->
					<h3 style="text-align: center; margin-bottom: 25px;">HUNGRY SNAKE</h3>

					<Snake/>

				{:else if overlayMode === "slider"}

					<Button text on:click={() => { setOverlayMode(null) }} class="grey-text text-darken-2">
						<Icon path={mdiClose} style="margin-right: 5px;" /> close
					</Button>

					<!-- <h3 style="text-align: center; letter-spacing: 8px; margin-bottom: 25px; color: #f3d161">SLIDER PUZZLE</h3> -->
					<!-- <h3 style="text-align: center; margin-bottom: 25px;">🧩 SLIDER PUZZLE 🧩</h3> -->
					<h3 style="text-align: center; margin-bottom: 25px;">SLIDER PUZZLE</h3>

					<Slider/>

				{:else if overlayMode === "battle"}

					<Button text on:click={() => { setOverlayMode(null) }} class="grey-text text-darken-2">
						<Icon path={mdiClose} style="margin-right: 5px;" /> close
					</Button>

					<!-- <h3 class="blue-text text-lighten-2" style="text-align: center; letter-spacing: 8px; margin-bottom: 25px;">BATTLESHIP</h3> -->
					<!-- <h3 style="text-align: center; margin-bottom: 25px;">⚓ BATTLESHIP ⚓</h3> -->
					<h3 style="text-align: center; margin-bottom: 25px;">BATTLESHIP</h3>

					<div id="game-container">
						<BSGrid
							ref={condition == "placement" ? 'grid-1' : 'grid-2'}
							bind:this={playerGridEl}
							bind:selectedShip {orientation}
							bind:hasOverlap bind:ships={playerShips}
							guesses={opponentGuesses}
							{condition}
						/>
					
						<div id="ship-placement" class:disable={condition == 'game'}>
							<BSShipSelect bind:ships={playerShips} bind:selectedShip />
							<hr>
							<BSPlacementOption on:clear={() => clearShips()} on:random={() =>
								playerGridEl.placeRandom()} {condition}></BSPlacementOption>
							<hr>
							<BSOrientationBtn bind:orientation />
						</div>
					
						<BSGrid ref={condition == "placement" ? 'grid-2' : 'grid-1'}
							bind:this={opponentGridEl} bind:ships={opponentShips} {condition}
							hideShips={true} on:turn={(e) => handleTurn(e.detail)}
							guesses={playerGuesses}
							{activePlayer}
						/>
					
						<!-- <BSMessages ref="messages" bind:this={messagesEl} {hasOverlap} {numOfShipsPlaced} {condition}/> -->
					
						<BSStartNew ref="startNew" on:start={() => handleStart()} on:newGame={() =>
							reset()} {canStartGame} {condition} ></BSStartNew>
					
						{#if winner()}
							<BSWonLost ref="grid-1" {winner}></BSWonLost>
						{/if}
					</div>

				{:else if overlayMode === "breakout"}

					<Button text on:click={() => { setOverlayMode(null) }} class="grey-text text-darken-2">
						<Icon path={mdiClose} style="margin-right: 5px;" /> close
					</Button>

					<!-- <h3 style="text-align: center; letter-spacing: 8px; margin-bottom: 25px;" class="green-text text-lighten-2">BREAKOUT</h3> -->
					<!-- <h3 style="text-align: center; margin-bottom: 25px;">👾 BREAKOUT 👾</h3> -->
					<h3 style="text-align: center; margin-bottom: 25px;">BREAKOUT</h3>

					<Card style="padding: 10px; width: 650px; background-color: {theme === 'dark' ? '#151515' : 'grey lighten-5'};" >
						<Breakout/>
					</Card>
					<p style="text-align: center; margin: 10px; color: #757575"><i>Use the left and right arrow keys to control the paddle.</i></p>

				{/if}

			</Overlay>

		</div>

	</MaterialApp>

<style>
	.container {
		height: auto;
		/* width: 100vw; */
		background-image: url("/assets/rainbow-background.svg");
		background-size: contain;
		background-repeat: no-repeat;
	}
	#math {
		width: 45vw;
	}
	.footer-items {
		display: flex;
	}
	.main-container {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
	b:hover {
		cursor: pointer;
		text-decoration: underline;
	}
	#login-container {
		width: 400px;
	}
	/* #tetris-container {
		height: 80vh;
		width: 550px;
		border-radius: 10px;
		margin-bottom: 80%;
	} */

	h4 {
		margin: 30px 0;
		font-size: 20pt;
		font-weight: bold;
		left: 0;
	}
	header {
        /* background-color: #161616; */
        height: 60px;
        width: 100vw;
        display: flex;
        position: fixed;
        justify-content: space-between;
        padding: 0;
        margin: 0;
        /* box-shadow: rgba(0, 0, 0, 0.5) 0px 2px 8px 0px;    */
        z-index: 100; 
    }
    #logo {
        height: 62px;
        padding: 12.5px;
    }
    .nav-buttons {
        padding: 12.5px;
        display: flex;
		min-width: 130px;
		/* width: auto; */
        /* justify-content: space-between; */
    }
	
    #main {
        /* color: white; */
        font-size: 10pt;
        text-align: center;
    }
    b {
        color: #FF4E4E;
    }
    
	.game-container {
        display: flex;
        flex-direction: column;
        height: 100vh;
        justify-content: center;
		margin-right: 3%;
		width: 40%;
    }
    .row {
        display: flex;
        justify-content: center;
    }
	
    #game-container {
        width: 850px;
        height: 640px;
        margin: auto;
        display: grid;
        grid-gap: 30px;
        grid-template-columns: repeat(11, 1fr);
        grid-template-rows: repeat(8, 1fr);
        grid-template-areas:
            "a a a a a a a a b b b"
            "a a a a a a a a b b b"
            "a a a a a a a a b b b"
            "a a a a a a a a b b b"
            "a a a a a a a a e e e"
            "a a a a a a a a d d d"
            "a a a a a a a a d d d"
            "a a a a a a a a d d d"
    }
    :global([ref=grid-1]) {
        grid-area: a;
    }
    :global([ref=grid-2]) {
        grid-area: d;
    }
    #ship-placement {
        grid-area: b;
    }
    :global([ref=messages]) {
        grid-area: c;
    }
    :global([ref=startNew]) {
        grid-area: e;
    }
    .disable {
        pointer-events: none;
    }


	@media screen and (max-width: 1024px){
		.main-container {
			flex-direction: column;
			height: auto;
		}
		.container {
			background-image: url("/assets/rainbow-bg-mobile.png");
			background-size: cover;
			height: 100vh;
		}
		.game-container {
			height: auto;
			margin-right: 0;
			width: 100%;
		}
		#math {
			width: 94vw;
		}
	}
    /* .game {
        height: 225px;
        width: 225px;
        background-color: gray;
        margin: 0 10px;
        border-radius: 100em;
        display: flex;
        flex-direction: column;
        justify-content: center;
        text-align: center;
        font-size: 26px;
		box-shadow: rgba(0, 0, 0, 0.3) 0px 3px 8px;
		transition: 0.2s ease-in-out;
    }
    .tetris {
        background-color: #FF4E4E;
        color: #fc9f9f;
    }
    .wildwest {
        background-color: #FF9452;
        color: #fdc29c;
    }
    .nonograms {
        background-color: #f3d161;
        color: #fff4d1;
    }
    .twenty {
        background-color: #7eabff;
        color: #c8dbff;
    }
    .breakout {
        background-color: #77da5f;
        color: #dffdd8;
    }
    .game:hover {
        text-decoration: none;
        transform: scale(1.1);
		cursor: pointer;
    } */

	
	.message *{
		margin-right: 10px;
		display: inline;
	}
</style>