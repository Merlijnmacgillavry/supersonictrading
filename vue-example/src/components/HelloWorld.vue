<template>
    <div id="helloworld" class="bg-dark">
        <div class="container">
            <div class="row">
                <div class="col-3">
                    <div class="box">
                        <h1>Current Day: {{currentDay}}</h1>
                        <h2>TimeLeft: {{timeLeft}}</h2>
                        <hr>
                        <h1>News</h1>
                        <div class="list-group">
                            <div class="list-item" v-for="(h, index) in headlines">
                                <p class="news" style="" v-if="index < 5 || 0">{{h}} </p>
                                <!--<p class="news" style="" v-if="index < 5 || 0">{{content[index]}} </p>-->
                                <p class="news" style="border-bottom: 2px dotted white; color: gold" v-if="index < 5 || 0">{{source[index]}} </p>

                            </div>
                        </div>
                    </div>
                    <div class="box">
                        <h1>Account: </h1>
                        <h2>Start Capital: {{parseFloat(startCap)}}</h2>
                        <h2>Balance: {{ parseFloat(capital).toFixed(3) }}</h2>
                        <h2>Shared Capital: {{ parseFloat(sharesCap).toFixed(3)}}</h2>
                        <h2>Total Capital: {{ parseFloat(sharesCap + capital).toFixed(3)}}</h2>
                        <h2>Net Profit: {{ parseFloat(capital + sharesCap - startCap).toFixed(3)}}</h2>
                        <h2>Profit: {{ parseFloat((capital +sharesCap - startCap)/startCap*100).toFixed(3)}} %</h2>
                    </div>
                </div>
                <div class="col-6">
                    <h3 class="box"><b style="color: #5288ff">All Companies</b> </h3>


                    <table class="table box">
                        <thead>
                        <tr>
                            <th>Logo</th>
                            <th>Name</th>
                            <th>#Shares</th>
                            <th>Shares Value</th>
                            <th>Price</th>
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
                            <th><button class="buy"  @click="buy(c.id)">B</button></th>
                            <th><button class="sell"  @click="sell(c.id)">S</button></th>
                            <th><button class="liq"  @click="liquadate(c.name, c.id)">0</button></th>
                        </tr>
                        </tbody>
                    </table>


                </div>
                <div class="col-3">
                    <h3 class="box"><b style="color: #5288ff">News Companies</b> </h3>
                    <table class="table box">
                        <thead>
                        <tr>
                            <th>Logo</th>
                            <th>#Shares</th>
                            <th>Price</th>
                            <th>BM</th>
                            <th>SM</th>
                            <th>LI</th>

                        </tr>

                        </thead>
                        <tbody>
                        <tr v-for="(c, index) in inNews" v-if="index <5" >
                            <th><img class="img-fluid" style="height: 40px; border-radius: 50%; margin-right: 10px" v-bind:src="c.logo" alt="c.name"></th>

                            <th>{{getAmountOfShares(c.name) || 0}}</th>

                            <th>{{parseFloat(c.value).toFixed(3)
                                }}</th>
                            <th><button class="buy"  @click="buy(c.id)">B</button></th>
                            <th><button class="sell"  @click="sell(c.id)">S</button></th>
                            <th><button class="liq"  @click="liquadate(c.name, c.id)">0</button></th>
                        </tr>
                        </tbody>
                    </table>
                    <h3 class="box"><b style="color: #5288ff">Owned Companies</b> </h3>
                    <table class="table box">
                        <thead>
                        <tr>
                            <th>Logo</th>
                            <th>#Shares</th>
                            <th>Price</th>
                            <th>BM</th>
                            <th>SM</th>
                            <th>LI</th>

                        </tr>

                        </thead>
                        <tbody>
                        <tr v-for="(c, index) in owned"  >
                            <th><img class="img-fluid" style="height: 40px; border-radius: 50%; margin-right: 10px" v-bind:src="c.logo" alt="c.name"></th>

                            <th>{{getAmountOfShares(c.name) || 0}}</th>

                            <th>{{parseFloat(c.value).toFixed(3)
                                }}</th>
                            <th><button class="buy"  @click="buy(c.id)">B</button></th>
                            <th><button class="sell"  @click="sell(c.id)">S</button></th>
                            <th><button class="liq"  @click="liquadate(c.name, c.id)">0</button></th>
                        </tr>
                        </tbody>
                    </table>
                </div>

            </div>
            </div>
        <audio id="audio" src="../assets/sonicring.mp3"></audio>
        </div>

</template>

<script>
	import * as api from "../api";

	export default {
		name: "HelloWorld",

		data: () => ({
            startCap : 1055000,
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
            inNews: [],
            headlines: [],
            content: [],
            source: [],
            sharesCap: null,
            owned: [],
            timeLeft: null,
		}),

		methods: {
			async buy(company) {
				if (this.companyId) {
					try {
						const id = await api.placeImmediateBuyOrder(company, 100);
					} catch (e) {
						alert(e.message);
					}
				} else {
					alert(
						"Please wait for the first server response. (Did you fill in your credentials?)"
					);
				}
			},
			play() {
				console.log('playing');
				this.audio.play();

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
				this.companies = this.companies.sort((a, b) => { if(a.name < b.name) { return -1; }
					if(a.name > b.name) { return 1; }
					return 0;});
				this.player = game.player;
				this.currentDay = game.name.split(" ")[1];
                this.capital = game.player.capital;
                this.sharesCap = this.SharesCapital();
                this.getOwnedCompanies();
                this.timeLeft = game.endAt - game.startAt;



			},
            getOwnedCompanies(){
                var result = [];
                for(var c in this.companies){
                	for(var s in this.shares){
                		if(this.companies[c].name == this.shares[s].company.name){
                			result.unshift(this.companies[c]);
                        }
                    }
                }
                this.owned = result;
            },

            handleNewsUpdate(news){
				this.play();
                this.headlines.unshift(news.headline);
                this.content.unshift(news.content);
                this.source(news.source);
            },
            // getLatestNews(amount){
				// // var currentDay =
            // },
            SharesCapital(){
				var result =0;
			    for(var s in this.shares){
			    	result = result + (this.shares[s].amount * this.shares[s].company.value);
                }
                return result;
            },
			getAmountOfShares(company) {
				for ( var s in this.shares)
				{

					if (this.shares[s].company.name == company) {
						return this.shares[s].amount;
					}
				}
				return 0;
			},
            getNewsCompanies(headline, content) {
		            for (var c in this.companies) {
			            if (headline.toLowerCase().includes(this.companies[c].name.toLowerCase()) ) {
				            this.inNews.unshift(this.companies[c]);
			            }
		            }

            }

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
					this.getNewsCompanies(data.news.headline, data.news.content);
					this.handleNewsUpdate(data.news);
				},
				error: e => {
					console.error(e);
				}
			});
			if(localStorage.news){
				var news = ["test"]

            }
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
        font-size: 20px;

    }
    .liq{
        background-color: gold;
    }
</style>
