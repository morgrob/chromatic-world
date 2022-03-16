<script>
	import { showingLoginForm } from "../store"
    export let username = ''

	let isPresented = false;

	showingLoginForm.subscribe(value => {
		isPresented = value
	})
</script>

<div class="container">
    <div id="ovrlay"></div>
    <div id="form">
        <button id="cross">&#10005;</button>
        <h1>Welcome Back!</h1>
        <form>
            <input type="text" placeholder="username" class="txt" bind:value="{username}"/>
            <input type="password" placeholder="password" class="pswd"/>
            <input type="button" value="Log In" id="btn"/>
        </form>
    </div>
</div>

<style>
    form {
        display: flex;
        flex-direction: column;
    }
    input {
        margin: 10px;
        background-color: rgb(32, 32, 32);
        border: none;
        border-radius: 100em;
        padding: 10px 15px;
        color: white;
		width: 100%;
    }
    h1 {
        margin: 10px;
    }
#btn{
	background-color: #2ecc71;
	border: 2px solid #2ecc71;
	border: none;
	padding: 7px;
	color: #fff;
	margin: 20px;
	border-radius: 5px;
	font-size: 16
}
#btn:hover{
	background-color: #fff;
	color: #2ecc71;
	border: 2px solid #2ecc71
}
#ovrlay{
	background-color: #000;
	height: 100%;
	width: 100%;
	position: fixed;
	top: 0px;
	opacity: 0.7;
}
#form{
	background-color: #fff;
	width: 30%;
	margin: auto;
	border: 3px solid #2ecc71;
	border-radius: 10px;
	padding: 20px;
	text-align: center;
	position: relative;
	top: -310px;
}
#ovrlay,#form{display: none;}
#cross{
	position: absolute;
    left: 94%;
    top: 0px;
    color: #cc2e2e;
	font-size: 18;
	font-weight: bold;
	background-color: transparent;
	border: 0
}
h1{margin-top: 0px}
form,input[type="button"]{margin:0 !important}
.txt,.pswd{
	background-color: #fff;
	display: block;
	border: 2px solid #2ecc71;
	border-radius: 10px;
	padding: 5px;
	margin: auto;
	margin-bottom: 15px
}
</style>