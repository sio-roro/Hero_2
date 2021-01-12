<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link>
      <div v-if="isAuth">
        <router-link to="/about">About</router-link>
        <router-link to="/todo">Todo</router-link>
        <div class="username">Current User:{{ this.userInfo.displayName }}</div>
      </div>

      <div class="links">
        <a v-if="isAuth" @click="signOut" class="button--grey">signOut</a>
        <a v-else @click="signIn" class="button--green">signIn</a>
      </div>
    </div>
    <div v-if="isAuth">
      <router-view />
    </div>
  </div>
</template>
<script>
import firebase from "firebase";

export default {
  data() {
    return {
      isAuth: false,
      userInfo: null,
    };
  },
  mounted: function() {
    firebase.auth().onAuthStateChanged((user) => {
      this.isAuth = !!user;
      this.userInfo = user;
      console.log("User:", this.userInfo.displayName);
    });

    // it should be user data
  },

  methods: {
    signIn: function() {
      const provider = new firebase.auth.GoogleAuthProvider();
      firebase.auth().signInWithRedirect(provider);
    },
    signOut: function() {
      firebase.auth().signOut();
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
.button--green {
  color: #42b983;
}
.button--grey {
  color: #2c3e50;
}
</style>
