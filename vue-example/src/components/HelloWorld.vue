<template>
    <div id="helloworld" class="bg-dark">
        <div class="container">
            <div class="row">
                <div class="col-12"><h1>Have fun @ Coding MADness!</h1>
                    <p>Welcome: {{ name }}!</p>
                    <button @click="buy()">Buy 1 share of ING</button>
                    <h2>Latest news:</h2>
                    <p>{{news}}</p></div>
                <p>gameDay: {{Gamename}}</p>

            </div>
            <div  class="row">
                <div class="col-12">
                    <ul class="list-group">
                        <li v-for="c in companies" class="list-group-item">
                            <p><b>Name: </b>{{c.name}}          <b>sector:</b> {{c.sectorName}} <b>value: </b>{{parseFloat(c.value).toFixed(3)
                                }} <b>shares:</b>  {{getAmountOfShares(c.name)}}</p>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
	import * as api from "../api";

	export default {
		name: "HelloWorld",

		data: () => ({
			name: "[waiting for server]",
			companyId: null,
			news: "No latest news yet!",
			Gamename: null,
            companies: null,
            shares: null,
		}),

		methods: {
			async buy() {
				if (this.companyId) {
					try {
						const id = await api.placeImmediateBuyOrder(this.companyId, 1);
						alert("We bought a new share with id: " + id);
					} catch (e) {
						alert(e.message);
					}
				} else {
					alert(
						"Please wait for the first server response. (Did you fill in your credentials?)"
					);
				}
			},
			handleGameUpdate(game) {
				// For now we want to extract the companyId and player name
				this.name = game.player.name;
				this.companyId = game.companies.find(c => c.key === "ing").id;
				console.log(game);
				this.Gamename = game.name;
				this.companies = game.companies;
				this.shares = game.player.shares;
			},
			getAmountOfShares(company) {
				for(s in this.shares){
					if(s.company == company){
						return s.amount;
                    }
                }
            },
		},

		// This method is called once when the component is started
		mounted() {
			// Subscribe to game updates
			this.querySubscription = api.activeGameSubscription().subscribe({
				next: ({data}) => {
					// Handle the game update
					this.handleGameUpdate(data.activeGame);
				},
				error: e => {
					console.error(e);
				}
			});


			// Also subscribe to news updates
			this.newsSubscription = api.newsSubscription().subscribe({
				next: ({data}) => {
					// Show the news
					console.log(data);
					this.news = data.news.headline;
				},
				error: e => {
					console.error(e);
				}
			});
		},

		// This method is called once when the component is destroyed
		beforeDestroy() {
			// Always unsubscribe, to prevent hot-reloading bugs
			this.querySubscription.unsubscribe();
			this.newsSubscription.unsubscribe();
		}
	};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

    #helloworld{

        color: white;
    }
    .list-group{
        border: 2px solid gold;
        border-radius: 2px;

    }
    .list-group-item:nth-child(even){
        background-color: #61727b !important;
    }
    .list-group-item:nth-child(odd){
        background-color: #343a40!important;
    }
</style>
