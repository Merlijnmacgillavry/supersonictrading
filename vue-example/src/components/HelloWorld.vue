<template>
    <div id="helloworld" class="bg-dark">
        <div class="container">
            <div class="row">
                <div class="col-8">
                    <div class="box">
                        <h1>Current Day: {{currentDay}}</h1>
                        <hr>
                        <h1>News</h1>
                        <p class="news">{{news}}</p>
                    </div>
                </div>
                <div class="col-4">
                    <div class="box">
                        <h1>Account: </h1>
                        <h2> balance: {{ capital }}</h2>
                        <h2>Net Profit: {{ parseFloat(capital - 1000000).toFixed(3)}}</h2>
                        <h2>Profit: {{ parseFloat((capital - 1000000)/1000).toFixed(3)}} %</h2>
                    </div>
                </div>
            </div>
            <div class="row">
                <div class="col-12">

                    <!--<button @click="buy()">Buy 1 share of ING</button>-->

                    <!--<button @click="sell()">Sell 1 share of ING</button>-->


                </div>
            </div>
            <div class="row">
                <div class="col-12">
                    <table class="table">
                        <thead>
                         <tr>
                             <th>Logo</th>
                             <th>Name</th>
                             <th>#Shares</th>
                             <th>Shares Value</th>
                             <th>Price</th>
                             <th>BL</th>
                             <th>SL</th>
                             <th>amount</th>
                             <th>BM</th>
                             <th>SM</th>
                             <th>LI</th>

                         </tr>

                        </thead>
                        <tbody>
                        <tr v-for="(c, index) in companies" >
                            <th><img class="img-fluid" style="height: 40px; border-radius: 50%; margin-right: 10px" v-bind:src="c.logo" alt="c.name"></th>
                            <th>{{c.name}}</th>
                            <th>{{getAmountOfShares(c.name) || 0}}</th>
                            <th>{{parseFloat(c.value * getAmountOfShares(c.name)).toFixed(3)
                                }}</th>
                            <th>{{parseFloat(c.value).toFixed(3)
                                }}</th>
                            <th><button class="buy"> B </button></th>
                            <th><button class="sell"> S </button></th>
                            <th>
                                <!--<input type="text" v-bind:value="amounts[index].amount">-->
                            </th>
                            <th><button class="buy"  @click="buy(c.id)">B</button></th>
                            <th><button class="sell"  @click="sell(c.id)">S</button></th>
                            <th><button class="liq"  @click="liquadate(c.name, c.id)">0</button></th>
                        </tr>
                        </tbody>
                    </table>

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
			player: null,
            currentDay: null,
            capital: null,
            amounts: [],
		}),

		methods: {
			async buy(company) {
				if (this.companyId) {
					try {
						const id = await api.placeImmediateBuyOrder(company, 100);
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
            makeAmounts(companies){
			  for(var c in companies){
				  var amount  = {
					  company: companies[c].name,
					  amount :0
				  }
              }
              amounts.push(amount);
            },
			async sell(company) {
				if (this.companyId) {
					try {
						const id = await api.placeImmediateSellOrder(company, 100);
						alert("We sold: " + id);
					} catch (e) {
						alert(e.message);
					}
				} else {
					alert(
						"Please wait for the first server response. (Did you fill in your credentials?)"
					);
				}
			},

			async liquadate(company,i) {
				console.log(company);
				var amount_shares = this.getAmountOfShares(company, i);
				console.log(this.getAmountOfShares(company));

					if (amount_shares > 0 && amount_shares >= 100) {
						if (this.companyId) {
							try {
								const id = await api.placeImmediateSellOrder(i, 100);
								alert("liq top");
							} catch (e) {
								alert(e.message);
							}
						} else {
							alert(
								"Please wait for the first server response. (Did you fill in your credentials?)"
							);
						}
					} else if (amount_shares > 0 && amount_shares < 100) {
						if (this.companyId) {
							try {
								const id = await api.placeImmediateSellOrder(i, amount_shares);
								alert("liq bot");
							} catch (e) {
								alert(e.message);
							}
						} else {
							alert(
								"Please wait for the first server response. (Did you fill in your credentials?)"
							);
						}
					} else if (amount_shares < 0 && amount_shares <= -100) {
						if (this.companyId) {
							try {
								const id = await api.placeImmediateBuyOrder(i, 100);
								alert("liq -top");
							} catch (e) {
								alert(e.message);
							}
						} else {
							alert(
								"Please wait for the first server response. (Did you fill in your credentials?)"
							);
						}
					}
					else if (amount_shares < 0 && amount_shares > 100) {
						if (this.companyId) {
							try {
								const id = await api.placeImmediateBuyOrder(i, -1 * amount_shares);
								alert("liq -bot");
							} catch (e) {
								alert(e.message);
							}
						} else {
							alert(
								"Please wait for the first server response. (Did you fill in your credentials?)"
							);
						}
					}





			},
			handleGameUpdate(game) {
				// For now we want to extract the companyId and player name
				this.name = game.player.name;
				this.companyId = game.companies.find(c => c.key === "ing").id;
				this.Gamename = game.name;
				this.companies = game.companies;
				this.shares = game.player.shares;
				this.player = game.player;
				this.currentDay = game.name.split(" ")[1];
                this.capital = game.player.capital;

			},
            // getLatestNews(amount){
				// // var currentDay =
            // },
			getAmountOfShares(company) {
				for ( var s in this.shares)
				{

					if (this.shares[s].company.name == company) {
						return this.shares[s].amount;
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

    #helloworld {

        color: white;
    }

    .table {
        max-height: 800px;
        border: 2px solid gold;
        border-radius: 2px;
        overflow-y: scroll;
        overflow-x: hidden;

    }

    .box {
        margin-top: 20px;
        padding: 20px;
        border: 2px solid gold;
        border-radius: 2px;
    }

    tr:nth-child(even) {
        background-color: #273138 !important;
    }

    tr:nth-child(odd) {
        background-color: #343a40 !important;
    }
    table{
        color: white;
    }
    button{
        width: 25px;
        text-align: center;
        border-radius: 50%;
        border-color: transparent;
        color: white;

    }
    .buy{
        background-color: green;
    }
    .sell{
        background-color: red;
    }
    .news{
        font-family: 'Volkhov', serif;

    }
    .liq{
        background-color: gold;
    }
</style>
