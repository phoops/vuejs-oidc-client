<template>
  <div id="app">
    <h1>Vue.js OIDC Client</h1>
    <div v-if="signedIn">
      <span>{{user.profile['http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name']}}</span>
      <button v-on:click="signOut()">Sair</button>
    </div>
  </div>
</template>

<script>
import Oidc from 'oidc-client'

export default {
  name: 'app',
  data () {
    return {
      user: null,
      signedIn: false,
      mgr: new Oidc.UserManager({
             userStore: new Oidc.WebStorageStateStore(),
             authority: 'https://identity.yourserver.com.br/core',
             client_id: 'your_client_id',
             redirect_uri: 'http://localhost:8080/static/callback.html',
             response_type: 'id_token token',
             scope: 'openid profile all_claims',
             post_logout_redirect_uri: 'http://localhost:8080/index.html',
             loadUserInfo: true
           })
    }
  },
  methods: {
    signIn () {
      this.mgr.signinRedirect().catch(function(err){
        console.log(err)
      })
    },
    signOut () {
      var self = this;
      this.mgr.signoutRedirect().then(function(resp) {
        self.signedIn = false
        console.log("signed out", resp);
      }).catch(function(err) { 
        console.log(err) 
      })
    },
    getUser () {
      let self = this
      this.mgr.getUser().then(function(user) {
        if (user == null) {
          self.signIn()
        } else {
          self.user = user
          self.signedIn = true
        }
      }).catch(function(err) {
        console.log(err)
      });
    }
  },
  mounted () {
    this.getUser()
  }
}

</script>

<style>
body {
  background-color: white;
}
</style>