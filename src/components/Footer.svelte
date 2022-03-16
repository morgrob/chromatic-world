<script>
    import { MaterialApp } from 'svelte-materialify';
</script>

<MaterialApp>
    <div class="container">
        <p class="main">Made with <b>&hearts;</b> by Morgan Roberts &copy; 2022</p>
        <div class="links">
            <a href="/">About</a>
            <a href="/">Privacy Policy</a>
            <a href="/">Help</a>
        </div>
    </div>
</MaterialApp>

<style>
    .container {
        /* background-color: #161616; */
        padding: 30px;
    }
    .main {
        /* color: white; */
        font-size: 14pt;
        text-align: center;
    }
    b {
        color: #FF4E4E;
    }
    .links a {
        padding: 0 15px;
        color: #848484;
    }
    .links {
        text-align: center;
    }
</style>