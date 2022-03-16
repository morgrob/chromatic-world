<script>
	// Import the functions you need from the SDKs you need
	import { initializeApp } from "firebase/app";
	// import { getAnalytics } from "firebase/analytics";
	import { getAuth, connectAuthEmulator, setPersistence, signInWithEmailAndPassword, createUserWithEmailAndPassword, signOut, browserLocalPersistence, updateProfile, onAuthStateChanged } from "firebase/auth";

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
	import Games from "./components/Games.svelte";

	import { Menu, List, ListItem, TextField, Icon, Button, Overlay, MaterialApp } from 'svelte-materialify';
	import { mdiInvertColors, mdiAccountCircle, mdiLock, mdiEyeOff, mdiEye, mdiAt, mdiLogout } from '@mdi/js';

	let theme = 'dark';

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
    	(v) => v.length >= 3 || 'Minimum 3 characters.',
    	(v) => {
			const pattern = /^[a-zA-Z\-]+$/
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

		try {
			await signInWithEmailAndPassword(auth, loginEmail, loginPassword);
			active = false;
		}
		catch(error) {
			console.log(error);
		}
	}

	const createEmailPassword = async () => {
		const loginEmail = email;
		const loginPassword = password;

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

</script>

	<MaterialApp theme="{theme}">

		<div class="container">

			<header>
				<img id="logo" src="assets/favicon.png" alt="chromatic.world logo">
				<div class="nav-buttons">
					{#if mode === 'loggedIn'}
						<Menu hover>
							<div slot="activator" style="margin-top: 8px;" class="black-text">
								<!-- <Button class="pink accent-1" style="margin-top: 12px;"> -->
									<Icon path={mdiAccountCircle} style="margin-right: 5px;" class="black-text"/>
									{username}
								<!-- </Button> -->
							</div>
							<List style="padding: 0;">
								<ListItem on:click="{logoutEmailPassword}" style="height: 55px;">
									<Icon path={mdiLogout}/>
									Log out
								</ListItem>
							</List>
					  	</Menu>
					{:else}
						<Button rounded class="button deep-purple accent-1 white-text" id="login" on:click={() => {active = true;}} style="margin-top: 2px; padding: 18px 20px">Log in</Button>
					{/if}
					
					<!-- <Button fab size="small" on:click="{toggleTheme}" style="margin-left: 20px;">
						<Icon path={mdiInvertColors}/>
					</Button> -->
				</div>
			</header>

			<Headline/>

			<Button fab size="small" on:click="{toggleTheme}" style="position: fixed; bottom: 20px; right: 20px;">
				<Icon path={mdiInvertColors}/>
			</Button>

			<Games/>

			<footer>
				<p id="main">Made with <b>&hearts;</b> by Morgan Roberts &copy; 2022</p>
				<div class="links">
					<a href="/">About</a>
					<a href="/">Privacy Policy</a>
					<a href="/">Help</a>
				</div>
			</footer>

			<Overlay opacity={0.98} {active}>
				<!-- <form class="login-container" on:submit|preventDefault={handleSubmit}> -->
				<form class="login-container" on:submit|preventDefault={loginEmailPassword}>
					{#if mode === 'signIn'}
					<h4>Welcome back!</h4>
					<p class="error">error message</p>
						<TextField class="deep-purple-text text-accent-1" rules={emailRules} bind:value={email}>
							<div slot="prepend-outer">
							<Icon path={mdiAt} />
							</div>
							Email
						</TextField>
						<br/>
						<!-- <TextField class="deep-purple-text text-accent-1" bind:value={username}>
							<div slot="prepend-outer">
							<Icon path={mdiAccountCircle} />
							</div>
							Username
						</TextField>
						<br/> -->
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
					<Button block on:click={() => {active = false;}}>Cancel</Button>
					{#if mode === 'signIn'}
						<p style="text-align: center;">Don't have an account? <b class="deep-purple-text text-accent-1" on:click={() => (mode = 'signUp')}>Create one.</b></p>
					{:else}
						<p style="text-align: center;">Already have an account? <b class="pink-text text-accent-1" on:click={() => (mode = 'signIn')}>Log in.</b></p>
					{/if}
				</form>
			</Overlay>

		</div>

	</MaterialApp>

<style>
	.error {
		display: none;
	}
	.container {
		height: auto;
		background-image: url("/assets/rainbow-bg.png");
		background-size: contain;
		background-repeat: no-repeat;
	}
	b:hover {
		cursor: pointer;
		text-decoration: underline;
	}
	.login-container {
		width: 400px;
	}
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
		/* width: auto; */
        /* justify-content: space-between; */
    }
	footer {
        padding: 30px;
    }
    #main {
        /* color: white; */
        font-size: 14pt;
        text-align: center;
    }
    b {
        color: #FF4E4E;
    }
    .links a {
        padding: 0 15px;
    }
    .links {
        text-align: center;
    }
</style>