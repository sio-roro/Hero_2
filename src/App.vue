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
      <div class="hamburger_btn" v-on:click="ActiveBtn = !ActiveBtn">
        <span
          class="line line_01"
          v-bind:class="{ btn_line01: ActiveBtn }"
        ></span>
        <span
          class="line line_02"
          v-bind:class="{ btn_line02: ActiveBtn }"
        ></span>
        <span
          class="line line_03"
          v-bind:class="{ btn_line03: ActiveBtn }"
        ></span>
      </div>
      <!--サイドバー-->
      <transition name="menu">
        <div class="menu" v-show="ActiveBtn">
          <div class="users" v-for="user in allUser" :key="user.id">
            <ul>
              <li>{{ user.userName }}</li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <div v-if="isAuth">
      <router-view />
    </div>
  </div>
</template>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
import firebase from "firebase";

export default {
  data() {
    return {
      isAuth: false,
      userInfo: null,
      ActiveBtn: false,
      allUser: [],
    };
  },
  mounted: function() {
    firebase.auth().onAuthStateChanged((user) => {
      this.isAuth = !!user;
      this.userInfo = user;
      console.log("User:", this.userInfo.displayName);
      firebase
        .firestore()
        .collection("User")
        .doc(this.userInfo.displayName)
        .set(
          {
            userId: this.userInfo.uid,
            userName: this.userInfo.displayName,
          },
          {
            merge: true,
          }
        )
        .then(function(result) {
          console.log("成功", result);
        });
    });
    var users = this.allUser;
    firebase
      .firestore()
      .collection("User")
      .get()
      .then(function(collection) {
        for (const doc of collection.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          users.push(todo);
          console.log("firebase:", doc.data());
        }
      });
  },
  created: function() {},

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

.hamburger_btn {
  position: fixed; /*常に最上部に表示したいので固定*/
  top: 0;
  right: 0;
  width: 70px;
  height: 72px;
  cursor: pointer;
  z-index: 50;
}

.hamburger_btn .line {
  position: absolute;
  top: 0;
  left: 20px;
  width: 32px;
  height: 2px;
  background: #333333;
  text-align: center;
}

.hamburger_btn .line_01 {
  top: 16px;
  transition: 0.4s ease;
}
.hamburger_btn .line_02 {
  top: 26px;
  transition: 0.4s ease;
}
.hamburger_btn .line_03 {
  top: 36px;
  transition: 0.4s ease;
}

.btn_line01 {
  transform: translateY(10px) rotate(-45deg);
  transition: 0.4s ease;
}
.btn_line02 {
  transition: 0.4s ease;
  opacity: 0;
}
.btn_line03 {
  transform: translateY(-10px) rotate(45deg);
  transition: 0.4s ease;
}

/*サイドバー*/
.menu-enter-active,
.menu-leave-active {
  transition: opacity 0.4s;
}
.menu-enter,
.menu-leave-to {
  opacity: 0;
}
.menu-leave,
.menu-enter-to {
  opacity: 1;
}

.menu li {
  list-style: none;
  line-height: 1;
  padding: 1rem;
}
.menu {
  background-color: rgba(197, 197, 197, 0.671);
  z-index: 30;
  padding: 2rem 1rem;
  position: fixed;
  width: 20rem;
  height: 80rem;
  top: 0;
  right: 0;
}
.menu a {
  color: rgb(66, 66, 66);
  text-decoration: none;
  font-size: 1.2rem;
  padding: 0 2rem;
}
.menu ul {
  margin: 1rem;
  padding: 0;
}
</style>
