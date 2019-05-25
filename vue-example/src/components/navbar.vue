<template>
    <div style="background-color: grey">
        <nav class="navbar navbar-expand-lg navbar-light bg-dark">
            <img src="../assets/logo.png" alt="" class="img-fluid">
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>

            <div class="collapse navbar-collapse" id="navbarSupportedContent">
                <ul class="navbar-nav mr-auto">
                    <li class="nav-item active">
                        <p>Super Sonic Trading</p>
                    </li>
                   
                </ul>

            </div>
        </nav>
    </div>
</template>

<script>
	export default {
		name: "Navbar"
	}
</script>

<style scoped>
    img{
        max-height: 80px;
    }
    nav{
        background-color: grey;
        border-bottom: 2px solid gold;
    }
    li{
        padding: 10px;
    }
    p{
        color: white;
        font-family: 'Orbitron', sans-serif;
        font-size: 26px;

    }
</style>