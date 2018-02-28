<template>
  <div id="app">
    <section class="section">
      <div class="container">
        <div v-if="!this.accessToken" class="notification is-warning">
          Please Enter your Client ID from the Spotify Developer Dashboard to generate a token.
        </div>
        <div class="columns">
          <div v-if="!this.accessToken" class="column">
            <nav class="panel">
              <p class="panel-heading">
                Authenticate
              </p>
              <div class="panel-block">
                <p class="control has-icons-left">
                  <input v-model="clientId" class="input is-small" type="text" placeholder="Client ID">
                  <span class="icon is-small is-left">
                    <i class="fa fa-key"></i>
                  </span>
                </p>
              </div>
              <div class="panel-block">
                <button v-on:click="getToken()" class="button is-link is-outlined is-fullwidth">
                  Generate Token
                </button>
              </div>
            </nav>
          </div>
          <div class="column">
            <nav class="panel">
              <p class="panel-heading">
                Search Artists
              </p>
              <div class="panel-block">
                <p class="control has-icons-left">
                  <input v-model="artist" @keyup.enter="getArtist" class="input is-small" type="text" placeholder="search">
                  <span class="icon is-small is-left">
                    <i class="fa fa-search"></i>
                  </span>
                </p>
              </div>
              <a v-for="track in tracks" class="panel-block is-active">
                <span class="panel-icon">
                  <i class="fa fa-music"></i>
                </span>
                {{ track.name }} &nbsp;
                <span class="panel-icon">
                  <i class="fa fa-user"></i>
                </span>
                {{ track.artists[0].name }}
                <audio
                  :src="track.preview_url"
                  controls>
                  Your browser does not support the <code>audio</code> element.
                </audio>
              </a>
            </nav>
          </div>
        </div>  
      </div>
    </section>
  </div>
</template>

<script>
import Axios from 'axios'

export default {
  name: 'app',
  data () {
    return {
      clientId: '',
      accessToken: this.getParameterByName('access_token'),
      config: [],
      artist: '',
      data: [],
      tracks: []
    }
  },
  methods: {
    getToken: function(){
      window.location = 'https://accounts.spotify.com/authorize?client_id=' + this.clientId + '&redirect_uri=http://localhost:8080&response_type=token';
    },
    getParameterByName: function(name, url) {
      if (!url) url = window.location.href;
      name = name.replace(/[\[\]]/g, "\\$&");
      var regex = new RegExp("[?#]" + name + "(=([^&#]*)|&|#|$)"),
          results = regex.exec(url);
      if (!results) return null;
      if (!results[2]) return '';
      return decodeURIComponent(results[2].replace(/\+/g, " "));
    },
    getArtist: function(){
      this.data = '';
      this.tracks = '';
      this.config = {headers: {'Authorization': 'Bearer ' + this.accessToken}};
      Axios.get('https://api.spotify.com/v1/search?q=' + this.artist + '&type=track,artist&market=US', this.config)
        .then(response => {
          this.data = response.data;
          this.tracks = response.data.tracks.items;
        })
        .catch(e => {
          console.log(error);
        })
    }
  }
};

</script>

<style>
  
</style>
