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
      <div class="container is-fluid">
        <div class="columns is-multiline">
          <div class="column is-6 is-offset-3 content-card has-text-centered">
            <div id="game-picker has-text-centered">
              <h1 class="title has-text-centered">
                First, enter your SteamID64 to load your games
              </h1>
              <h2 class="subtitle has-text-centered">
                <br />
                <br /><br />
                <p class="help">
                  Paste your profile URL in the tool below to get your
                  <code>steamID64 (Dec)</code> value
                </p>
                <p class="help">
                  NOTE: Make sure your games list is public on Steam.
                </p>
                <br />
                <a
                  href="https://steamidfinder.com/"
                  class="button is-grey is-small"
                  target="_blank"
                >
                  Get your Steam64 (Dec) ID Here
                </a>
                <br /><br />
              </h2>
              <div class="control ">
                <input
                  v-model="steam64"
                  class="input is-medium"
                  type="text"
                  placeholder="76561198065418715"
                />
              </div>
              <br />
              <button class="button is-primary" @click="fetchGames(steam64)">
                Load Games by ID
              </button>
              <hr />
              <div class="buttons is-centered">
                <button
                  class="button is-light is-small"
                  @click="fetchGames('76561198065418715')"
                >
                  Load 0NEGUY's Games
                </button>
                <button
                  class="button is-light is-small"
                  @click="fetchGames('76561198111514784')"
                >
                  Load Enszourous' Games
                </button>
                <button
                  class="button is-light is-small"
                  @click="fetchGames('76561198049210825')"
                >
                  Load JesseGR's Games
                </button>
                <button
                  class="button is-light is-small"
                  @click="fetchGames('76561198065418715')"
                >
                  Load Goat's Games
                </button>
              </div>
            </div>
          </div>
          <div v-if="steamData" class="column is-4 is-offset-4 content-card">
            <div class="columns is-multiline">
              <div class="column is-12 has-text-centered">
                <button @click="pickRandomGame" class="button is-info">
                  Pick a Random Game
                </button>
                <br /><br />
              </div>
              <div v-if="randomChosen" class="column  has-text-centered">
                <div class="game-card">
                  <div class="game-card--head">
                    <img
                      :src="getGameBanner(randomChosen.appID[0])"
                      @error="handleBadBanner"
                      alt=""
                    />
                  </div>
                  <div class="game-card--body">
                    <h2 class="subtitle">{{ randomChosen.name[0] }}</h2>
                  </div>
                </div>
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
                    <img
                      :src="getGameBanner(game.appID[0])"
                      @error="handleBadBanner"
                      alt=""
                    />
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
import { ref } from "vue";
import axios from "axios";
export default {
  name: "Home",
  setup() {
    const loading = ref(false);
    const steam64 = ref(undefined);

    const steamData = ref(undefined);
    const fetchGames = (profile) => {
      loading.value = true;
      // ! didn't want to deal with cors in localhost development
      const URL = `https://cors-anywhere.herokuapp.com/steamcommunity.com/profiles/${profile}/games?tab=all&xml=1`;
      const xml2js = require("xml2js");
      var parser = new xml2js.Parser();
      axios.get(URL).then((response) => {
        parser
          .parseStringPromise(response.data)
          .then((result) => {
            loading.value = false;
            steamData.value = result.gamesList.games[0].game;
          })
          .catch(() =>
            this.displayError("Invalid SteamID or game list is set to private.")
          );
      });
    };

    const getGameBanner = (appID) => {
      let url = `https://cdn.akamai.steamstatic.com/steam/apps/${appID}/header.jpg`;
      return url;
    };

    const randomChosen = ref(undefined);

    const pickRandomGame = () => {
      randomChosen.value =
        steamData.value[Math.floor(Math.random() * steamData.value.length)];
    };

    const handleBadBanner = (event) => {
      event.target.src = "https://cdn.oneguy.io/default-banner.jpg";
    };

    return {
      loading,
      steam64,
      steamData,
      fetchGames,
      randomChosen,
      getGameBanner,
      pickRandomGame,
      handleBadBanner,
    };
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
  .game-card-head {
    background: url("../assets/default-banner.jpg");
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center;
    min-height: 175px;
    img {
      width: 100%;
    }
  }
  .game-card--body {
    background: ghostwhite;
    text-align: center;
    padding: 1rem;
  }
}
.card {
  box-shadow: none;
  border: 1px solid #e1e1e1;
  border-radius: 10px;
  .card-body {
    padding: 3rem 1rem;
    .title {
      font-weight: 400;
    }
    .subtitle {
      font-size: 1rem;
      margin-bottom: 1.75rem;
    }
  }
}
</style>
