<template>
  <div class="home">
    <section class="hero is-primary">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">
            Random Game Picker
          </h1>
          <h2 class="subtitle">
            Get a random game from your Steam library to play
          </h2>
        </div>
      </div>
    </section>
    <section id="home">
      <div class="container">
        <div class="columns is-multiline">
          <div class="column is-12 content-card">
            <div id="game-picker">
              <h2 class="subtitle">
                Enter your SteamID64 below to get your games list
              </h2>
              <b-field label="SteamID64">
                <b-input v-model="steam64"></b-input>
              </b-field>
              <b-button @click="fetchGames">Load my Games</b-button>
            </div>
          </div>
          <div v-if="steamData" class="column is-12 content-card">
            <div class="columns is-multiline">
              <div class="column is-12">
                <button
                  @click="pickRandomGame"
                  class="button is-primary is-large"
                >
                  Pick Random Game
                </button>
              </div>
              <div v-if="randomChosen" class="column is-12">
                <h1 class="title">You're going to play:</h1>
                <h1 class="title">{{ randomChosen.name[0] }}</h1>
              </div>
            </div>
          </div>
          <div v-if="steamData" class="column is-12 content-card">
            <div class="columns is-multiline">
              <div
                v-for="game in steamData"
                :key="game.appID"
                class="column is-3"
              >
                <div class="game-card">
                  <div class="game-card--head">
                    <img :src="game.logo[0]" alt="" width="100%" />
                  </div>
                  <div class="game-card--body">
                    <h2 class="subtitle">{{ game.name[0] }}</h2>
                  </div>
                </div>
              </div>
            </div>
            {{ steamData[0] }}
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Home",
  components: {},
  data: () => ({
    steam64: "76561198065418715",
    steamData: undefined,
    randomChosen: undefined,
  }),
  methods: {
    fetchGames() {
      const id = this.steam64;
      const URL = `https://cors-anywhere.herokuapp.com/steamcommunity.com/profiles/${id}/games?tab=all&xml=1`;
      const parseString = require("xml2js").parseString;
      console.log(URL);

      axios.get(URL).then((response) => {
        parseString(response.data, (err, result) => {
          if (err) {
            alert(err);
          } else {
            this.steamData = result.gamesList.games[0].game;
          }
        });
      });
    },
    pickRandomGame() {
      this.randomChosen = this.steamData[
        Math.floor(Math.random() * this.steamData.length)
      ];
    },
  },
};
</script>

<style lang="scss">
#home {
  background: #edecff;
  min-height: 100vh;
}
.hero {
  margin-bottom: 5rem;
}
.content-card {
  padding: 2rem;
  background: white;
  border-radius: 10px;
  margin-top: 2rem;
}
.game-card {
  border: 1px solid #00024a;
  border-radius: 5px;
  background: ghostwhite;
  overflow: hidden;
  .game-card--body {
    background: ghostwhite;
    text-align: center;
    padding: 1rem;
  }
}
</style>
