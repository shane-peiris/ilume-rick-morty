<template>
  <div id="app">
    <div class="position-fixed">
      <div class="hero is-dark is-gradient is-bold mb-5">
        <div
          class="hero-body"
          style="
            padding-top: 2.5rem;
            padding-bottom: 0.5rem;
            padding-left: 1.5rem;
            padding-right: 1.5rem;
          "
        >
          <div class="flex">
            <h3 class="title mr-10">
              <span class="has-text-success">THE RICK AND MORTY API </span
              >&nbsp;
              <p class="subtitle">
                <small> https://rickandmortyapi.com/</small>
              </p>
            </h3>
            <div class="field has-addons">
              <div class="control searchinput">
                <input
                  v-model="search"
                  type="text"
                  class="input is-rounded width-50"
                  v-on:keyup.enter="searchData"
                />
              </div>
              <div class="control">
                <button
                  class="button is-success is-rounded"
                  v-on:click="searchData"
                >
                  Search
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div>
        <div class="center-flex" v-if="loadingIcon">
          <div class="loader-custom"></div>
        </div>
      </div>
    </div>

    <div class="container">
      <div
        class="columns is-desktop is-mobile is-tablet is-multiline is-centered"
      >
        <character
          @showModal="showModal"
          @loading="loadingHandle"
          v-for="character of characters"
          v-bind:key="character.id"
          v-bind:character="character"
        />
      </div>

      <nav
        class="pagination is-centered"
        role="navigation"
        aria-label="pagination"
      >
        <ul class="pagination-list">
          <li v-if="page != 1">
            <a class="pagination-previous" v-on:click="changePage(page - 1)"
              >Previous</a
            >
          </li>
          <li>
            <a class="pagination-link is-current">{{ page }} / {{ pages }}</a>
          </li>
          <li v-if="page != pages">
            <a class="pagination-next" v-on:click="changePage(page + 1)"
              >Next</a
            >
          </li>
        </ul>
      </nav>
    </div>

    <div class="modal" :class="{ 'is-active': modal }" v-if="modal">
      <div class="modal-background" @click="modal = false"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <figure class="image is-64x64 mr-3">
            <img
              class="is-rounded"
              v-bind:src="currentCharacter.image"
              v-bind:alt="currentCharacter.name"
            />
          </figure>
          <p class="modal-card-title">{{ currentCharacter.name }}</p>
          <button
            class="delete"
            @click="modal = false"
            aria-label="close"
          ></button>
        </header>
        <div class="modal-card-body" style="padding: 0px">
          <div class="display-inline mb-4">
            <div style="width: 60%" class="float-left">
              <img
                class="is-rounded"
                :src="img"
                v-bind:alt="currentCharacter.name"
                style="widows: 100%"
              />
            </div>
            <div
              style="width: 40%; padding-left: 20px; padding-top: 20px"
              class="float-right"
            >
              <p>
                Status: <strong>{{ currentCharacter.status }}</strong>
              </p>
              <p>
                Species: <strong>{{ currentCharacter.species }}</strong>
              </p>
              <p>
                Type:
                <strong>{{
                  currentCharacter.type == "" ? "-" : currentCharacter.type
                }}</strong>
              </p>
              <p>
                Gender: <strong>{{ currentCharacter.gender }}</strong>
              </p>
              <p>
                Episodes: <strong>{{ currentCharacter.episode.length }}</strong>
              </p>
            </div>
          </div>
          <div>
            <article
              v-if="showEpisodes"
              class="message is-primary mb-4"
              style="margin-top: 13.4rem; padding: 10px"
            >
              <div class="message-header">
                <p>Episode List</p>
                <button @click="showEpisodes = false" class="delete" aria-label="delete"></button>
              </div>
              <div class="message-body" style="margin-left: 20px">
                <ol>
                  <li v-for="item in episodeList" :key="item">
                    {{ item }}
                  </li>
                </ol>
              </div>
            </article>
          </div>
        </div>
        <footer class="modal-card-foot">
          <buttton
            v-if="!showEpisodes"
            class="button"
            @click="showEpisodes = true"
            >Show Episodes</buttton
          >
          <buttton
            v-if="showEpisodes"
            class="button"
            @click="showEpisodes = false"
            >Hide Episodes</buttton
          >
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import character from "./components/Character";
import img from "./assets/images/wp.jpg";

export default {
  name: "app",
  components: {
    character,
  },
  data: function () {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: "",
      modal: false,
      loadingIcon: false,
      showEpisodes: false,
      episodeList: [],
      img,

      currentCharacter: {},
    };
  },
  created() {
    this.fetch();
  },
  methods: {
    fetch() {
      const params = {
        page: this.page,
        name: this.search,
      };
      axios
        .get("https://rickandmortyapi.com/api/character", { params })
        .then((res) => {
          this.characters = res.data.results;
          this.pages = res.data.info.pages;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;
      this.fetch();
    },
    searchData() {
      this.page = 1;
      this.fetch();
    },
    showModal(id) {
      this.fetchOne(id);
    },
    loadingHandle() {
      this.loadingIcon = !this.loadingIcon;
    },

    async fetchOne(id) {

      this.episodeList = [];

      let result = await axios.get(
        `https://rickandmortyapi.com/api/character/${id}/`
      );

      this.currentCharacter = result.data;

      var episodeURLs = this.currentCharacter.episode;

      for (
        var episodeCount = 0;
        episodeCount < Object.values(episodeURLs).length;
        episodeCount++
      ) {
        var currentEpisode = episodeURLs[episodeCount];

        let result = await axios.get(currentEpisode);

        var currentEpisodeData = result.data;

        this.episodeList.push(currentEpisodeData.episode);
      }
      this.loadingHandle();
      this.modal = true;
    },
  },
};
</script>

