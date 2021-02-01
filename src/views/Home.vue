<template>
  <div class="home">
    <b-loading :is-full-page="true" v-model="loading"></b-loading>

    <section class="hero is-primary">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">
            Random Game Picker
          </h1>
          <h2 class="subtitle">
            Get a random game from your Steam library to play
          </h2>
          <h3 class="heading">
            Made with 💙 and ☕ by
            <a href="https://twitter.com/0NEGUYY" style="color: ghostwhite"
              >@0NEGUYY</a
            >
          </h3>
        </div>
      </div>
    </section>
    <section id="home">
      <div class="container">
        <div class="columns is-multiline">
          <div class="column is-12 content-card has-text-centered">
            <div id="game-picker has-text-centered">
              <h1 class="title has-text-centered">
                Enter your SteamID64 below to get your games list.
              </h1>
              <h2 class="subtitle has-text-centered">
                Make sure your games list is public on Steam.
                <br />
                <br /><br />
                <p class="help">
                  Paste your profile URL in the tool below to get your
                  <code>steamID64 (Dec)</code> value
                </p>
                <a
                  href="https://steamidfinder.com/"
                  class="button is-text"
                  target="_blank"
                >
                  Get your Steam64 (Dec) ID Here
                </a>
                <br /><br />
              </h2>
              <b-field label="Your SteamID64">
                <b-input v-model="steam64"></b-input>
              </b-field>
              <br />
              <b-button @click="fetchGames(steam64)" type="is-primary"
                >Load Games by ID</b-button
              >
              <hr />
              <div class="buttons is-centered">
                <b-button
                  @click="fetchGames('76561198065418715')"
                  type="is-small is-light"
                  >Load 0NEGUY's Games</b-button
                >
                <b-button
                  @click="fetchGames('76561198111514784')"
                  type="is-small is-light"
                  >Load Enszourous' Games</b-button
                >
                <b-button
                  @click="fetchGames('76561198049210825')"
                  type="is-small is-light"
                  >Load JesseGR's Games</b-button
                >
                <b-button
                  @click="fetchGames('76561198065418715')"
                  type="is-small is-light"
                  >Load Goat's Games</b-button
                >
              </div>
            </div>
          </div>
          <div v-if="steamData" class="column is-12 content-card">
            <div class="columns is-multiline">
              <div class="column is-12 has-text-centered">
                <button
                  @click="pickRandomGame"
                  class="button is-primary is-large"
                >
                  Pick Random Game
                </button>
                <br /><br />
              </div>
              <div v-if="randomChosen" class="column is-12 has-text-centered">
                <h2 class="subtitle">You're going to play:</h2>
                <br />
                <img
                  :src="randomChosen.logo[0]"
                  alt=""
                  width="300px"
                /><br /><br />
                <h1 class="title">{{ randomChosen.name[0] }}</h1>
                <br /><br />
              </div>
            </div>
          </div>
          <div v-if="steamData" class="column is-12 content-card">
            <div class="columns is-multiline">
              <div
                v-for="game in steamData"
                :key="game.appID[0]"
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
    steam64: undefined,
    steamData: undefined,
    randomChosen: undefined,
    loading: false,
  }),
  methods: {
    fetchGames(profile) {
      this.loading = true;
      // didn't want to deal with cors in localhost development
      const URL = `https://cors-anywhere.herokuapp.com/steamcommunity.com/profiles/${profile}/games?tab=all&xml=1`;
      const xml2js = require("xml2js");
      var parser = new xml2js.Parser();
      axios.get(URL).then((response) => {
        parser
          .parseStringPromise(response.data)
          .then((result) => {
            this.loading = false;
            this.steamData = result.gamesList.games[0].game;
          })
          .catch(() =>
            this.displayError("Invalid SteamID or game list is set to private.")
          );
      });
    },
    pickRandomGame() {
      this.randomChosen = this.steamData[
        Math.floor(Math.random() * this.steamData.length)
      ];
    },
    displayError(message) {
      this.loading = false;
      this.$buefy.dialog.alert({
        title: "Error fetching games list",
        message: message,
        type: "is-danger",
        hasIcon: false,
        ariaRole: "alertdialog",
        ariaModal: true,
      });
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
