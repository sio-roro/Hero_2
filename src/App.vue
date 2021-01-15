<template>
  <div id="app">
    <div id="nav">
      <i class="fas fa-paw" v-on:click="showFriend"></i>
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
    <div class="contaner">
      <div class="sidebar" v-if="fridendIsShowen">
        <div class="sidebar-wrapper">
          <div class="sidebar-link-area">
            <!-- サイドバーメニュー -->
            <p>HELLO!!</p>
            <p>HELLO!!</p>
            <p>HELLO!!</p>
            <p>HELLO!!</p>
            <p>HELLO!!</p>
            <p>HELLO!!</p>
          </div>
        </div>
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
      fridendIsShowen: false,
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
    showFriend: function() {
      console.log("wow");
      this.fridendIsShowen = !this.fridendIsShowen;
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

.sidebar {
  background-color: #191970;
  height: 100%; /* サイドバーの高さ */
  width: 200px; /* サイドバーのwidthを指定 */
  max-width: 200px; /* widthの最大値 */
  opacity: 0.9.5; /* 透過する 0に近くほど透過する */
  position: fixed; /* 左上に要素を固定する(スクロールしても位置は固定される) */
  overflow-x: hidden; /* 横軸ではみ出た要素を非表示にする */
  box-sizing: border-box; /* paddingとborderを、widthとheightに含める */
  padding-left: 40px; /* サイドバー内のリンクの位置を右にずらす */
}

.sidebar-link-area {
  padding-top: 20px; /* サイドバーリンクの上部に空白を作る */
}

.sidebar-link {
  color: #ffffff; /* リンクの文字色を白に */
}

.sidebar-link:hover {
  color: #ffffff; /* マウスがリンクに乗った時も文字色を白に */
}
</style>
