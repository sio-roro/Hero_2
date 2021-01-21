<template>
  <div id="app">
    <div id="nav">
      <div class="links">
        <div v-if="isAuth">
          <router-link class="menu-list" to="/"
            ><i class="fas fa-home"></i>{{ this.userInfo.displayName }}
          </router-link>
        </div>
        <div class="login-menu">
          <a v-if="isAuth" @click="signOut" class="button--grey menu-list"
            >signOut</a
          >
        </div>
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
          <input type="text" v-model="keyword" placeholder="Search friend" />

          <div class="users" v-for="user in filteredUsers" :key="user.id">
            <router-link
              :to="{ path: `/user/${user.userId}` }"
              v-if="user.userId != userInfo.uid"
            >
              <li class="menu-list">{{ user.userName }}</li></router-link
            >
          </div>
        </div>
      </transition>
    </div>
    <div v-if="isAuth" class="router">
      <router-view />
    </div>
    <div v-else>
      <a @click="signIn" class="signIn ">signIn with Google</a>
    </div>
  </div>
</template>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue-simple-spinner@1.2.8/dist/vue-simple-spinner.min.js"></script>
<script>
import firebase from "firebase";

export default {
  data() {
    return {
      isAuth: false,
      userInfo: null,
      ActiveBtn: false,
      allUser: [],
      keyword: "",
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
        .doc(this.userInfo.uid)
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

  methods: {
    signIn: function() {
      const provider = new firebase.auth.GoogleAuthProvider();
      firebase.auth().signInWithRedirect(provider);
    },
    signOut: function() {
      firebase.auth().signOut();
      location.reload();
    },
    showFriend: function() {
      console.log("wow");
      this.fridendIsShowen = !this.fridendIsShowen;
    },
  },
  computed: {
    filteredUsers: function() {
      var users = [];

      for (var i in this.allUser) {
        var user = this.allUser[i];

        if (user.userName.indexOf(this.keyword) !== -1) {
          users.push(user);
        }
      }

      return users;
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
  background-color: #484848;
  width: 90%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.router {
  width: 100%;
}
body {
  background-color: #484848;
}
#nav {
  position: absolute;
  top: 0;
  left: 0;
  color: #fff;
  padding: 30px;
  font-size: 25px;
  display: flex;
  flex-direction: row;
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
  background: #008b8b;
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
  overflow: scroll;
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
.username {
  color: whitesmoke;
}
.menu-list:hover {
  color: #008b8b;
  transition: all 0.5s;
}

.signIn {
  font-weight: bold;
  padding: 10px;
  border: 2px solid #fff;
  color: #fff;
}
.signIn a {
  text-decoration: none;
}
.signIn:hover {
  color: #42b983;
  transition: all 0.5s;
}
.menu-list {
  color: #fff;
  text-decoration: none;
}
.links {
  display: flex;
  flex-direction: row;
}
.login-menu {
  padding-left: 10px;
}
</style>
