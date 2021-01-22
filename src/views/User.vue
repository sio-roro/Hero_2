<template>
  <div class="user">
    <div class="user-header">
      <h1>{{ nowUser.userName }}</h1>
      <div class="unFollow-btn" v-if="isFollowed" v-on:click="unfollow">
        <i class="fas fa-star unfollow-star"></i>
      </div>
      <div class="follow-btn" v-else v-on:click="follow">
        <i class="far fa-star follow-star"></i>
      </div>
    </div>

    <div class="follow-ex">
      Your todo can be checked only by your Liked User!
    </div>
    <div class="user-todo" v-if="isAllowed">
      <div class="user_todoA">
        <div class="todo-a-content">
          <div class="group-header">TodoA</div>
          <div class="user-todo-area">
            <div v-for="todo in userTodoA" :key="todo.id" class="user-todoCard">
              <router-link :to="{ path: `/show/${todo.id}` }">
                <i class="fas fa-comment add-link">{{ todo.comment }}</i>
              </router-link>
              <div class="user-done-item user-todo" v-if="todo.isDone">
                {{ todo.item }}
              </div>
              <div v-else class="user-todo">{{ todo.item }}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="user_todoB">
        <div class="todo-b-content">
          <div class="group-header">TodoB</div>
          <div class="user-todo-area">
            <div v-for="todo in userTodoB" :key="todo.id" class="user-todoCard">
              <router-link :to="{ path: `/show/${todo.id}` }">
                <i class="fas fa-comment add-link">{{ todo.comment }}</i>
              </router-link>
              <div class="user-done-item" v-if="todo.isDone">
                {{ todo.item }}
              </div>
              <div v-else class="user-todo">{{ todo.item }}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="user_todoC">
        <div class="todo-c-content">
          <div class="group-header">TodoC</div>
          <div class="user-todo-area">
            <div v-for="todo in userTodoC" :key="todo.id" class="user-todoCard">
              <router-link :to="{ path: `/show/${todo.id}` }">
                <i class="fas fa-comment add-link"></i>
              </router-link>
              <div class="user-done-item user-todo" v-if="todo.isDone">
                {{ todo.item }}
              </div>
              <div v-else class="user-todo">{{ todo.item }}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="user_todoD">
        <div class="todo-d-content">
          <div class="group-header">TodoD</div>
          <div class="user-todo-area">
            <div v-for="todo in userTodoD" :key="todo.id" class="user-todoCard">
              <router-link :to="{ path: `/show/${todo.id}` }">
                <i class="fas fa-comment add-link"></i>
              </router-link>
              <div class="user-done-itemuser-todo" v-if="todo.isDone">
                {{ todo.item }}
              </div>
              <div v-else class="user-todo">{{ todo.item }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import firebase from "firebase";
export default {
  name: "user",
  components: {},
  beforeRouteUpdate(to, from, next) {
    //再描画前のアクション

    next();
    location.reload();
    //再描画後のアクション
  },
  data() {
    return {
      nowUser: [],
      currentUser: [],
      userTodoA: [],
      userTodoB: [],
      userTodoC: [],
      userTodoD: [],
      isFollowed: null,
      isAllowed: null,
    };
  },
  created() {
    firebase
      .firestore()
      .collection("User")
      .doc(this.currentUser.uid)
      .get()
      .then((user) => {
        console.log("currentuser-friend", user.data());
      });
  },

  mounted: function() {
    firebase
      .firestore()
      .collection("User")
      .doc(this.$route.params["id"])
      .get()
      .then((user) => {
        console.log("userPage:", user.data());
        this.nowUser = user.data();
        console.log("This User:", this.nowUser.friend);
        if (this.nowUser.friend.includes(this.currentUser.uid)) {
          console.log("いるよ！！");
          this.isFollowed = true;
        } else {
          console.log("いない！！");
          this.isFollowed = false;
        }
      });
    firebase.auth().onAuthStateChanged((user) => {
      this.currentUser = user;
      console.log("current User:", this.currentUser.uid);
      firebase
        .firestore()
        .collection("User")
        .doc(this.currentUser.uid)
        .get()
        .then((user) => {
          console.log("currentuser-friend", user.data().friend);
          if (user.data().friend.includes(this.nowUser.userId)) {
            console.log("見れるよ！！");
            this.isAllowed = true;
          } else {
            console.log("見れない！！");
            this.isAllowed = false;
          }
        });
    });

    firebase
      .firestore()
      .collection("todoA")
      .where("userId", "==", this.$route.params["id"])
      .where("group", "==", "todoA")
      .get()
      .then((todos) => {
        for (const doc of todos.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.userTodoA.push(todo);
          console.log("firebase:", doc.data());
        }
      });
    firebase
      .firestore()
      .collection("todoA")
      .where("userId", "==", this.$route.params["id"])
      .where("group", "==", "todoB")
      .get()
      .then((todos) => {
        for (const doc of todos.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.userTodoB.push(todo);
          console.log("firebase:", doc.data());
        }
      });
    firebase
      .firestore()
      .collection("todoA")
      .where("userId", "==", this.$route.params["id"])
      .where("group", "==", "todoC")
      .get()
      .then((todos) => {
        for (const doc of todos.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.userTodoC.push(todo);
          console.log("firebase:", doc.data());
        }
      });
    firebase
      .firestore()
      .collection("todoA")
      .where("userId", "==", this.$route.params["id"])
      .where("group", "==", "todoD")
      .get()
      .then((todos) => {
        for (const doc of todos.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.userTodoD.push(todo);
          console.log("firebase:", doc.data());
        }
      });
  },

  methods: {
    follow() {
      firebase
        .firestore()
        .collection("User")
        .doc(this.nowUser.userId)
        .update(
          {
            friend: firebase.firestore.FieldValue.arrayUnion(
              this.currentUser.uid
            ),
          },
          {
            merge: true,
          }
        );
      console.log("いってるよ！");
      this.isFollowed = true;
    },
    unfollow() {
      firebase
        .firestore()
        .collection("User")
        .doc(this.nowUser.userId)
        .update(
          {
            friend: firebase.firestore.FieldValue.arrayRemove(
              this.currentUser.uid
            ),
          },
          {
            merge: true,
          }
        );
      console.log("削除したよ！");
      this.isFollowed = false;
    },
  },
};
</script>
<style>
body {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  font-family: Montserrat;
  background: #484848;
}
.follow-ex {
  color: #fff;
}
.follow-star {
  color: #fff;
  font-size: 30px;
}
.unfollow-star {
  color: #42b983;
  font-size: 30px;
}
#app {
  background-color: #484848;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: row;
}
.user-header {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: row;
}
.user {
  background-color: #484848;
}
.user-done-item {
  text-decoration: line-through;
  opacity: 0.5;
}
.user h1 {
  color: #fff;
}
.user-todo {
  width: 100%;

  display: flex;
  flex-wrap: wrap;
}
.user_todoA {
  width: 50%;
  height: 50%;
  min-height: 250px;
}
.user_todoB {
  height: 50%;
  width: 50%;
  min-height: 250px;
}
.user_todoC {
  height: 50%;
  width: 50%;
  min-height: 250px;
}
.user_todoD {
  height: 50%;
  width: 50%;
  min-height: 250px;
}
.user-todo-area {
  display: flex;
  align-items: center;
  justify-content: center;
}
.todo-a-content {
  border-bottom: 1px solid #008b8b;
  border-right: 1px solid #008b8b;
  min-height: 250px;
}
.todo-b-content {
  border-bottom: 1px solid #008b8b;
  border-left: 1px solid #008b8b;
  min-height: 250px;
}
.todo-c-content {
  border-top: 1px solid #008b8b;
  border-right: 1px solid #008b8b;
  min-height: 250px;
}
.todo-d-content {
  border-top: 1px solid #008b8b;
  border-left: 1px solid #008b8b;
  min-height: 250px;
}
.done-item {
  text-decoration: line-through;
}
.group-header {
  color: #fff;
  font-size: 30px;
}
.user-todoCard {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: row;
  margin: 10px;
  padding: 10px;
  border: 1px solid #7f7f7f;
  border-radius: 10px;
  background-color: #dcdcdc;
  min-width: 25%;
}
.add-link {
  color: #008b80;
  font-size: 25px;
  padding: 2px 7px 0px 10px;
}
.add-link:hover {
  color: #42b983;
  transition: all 0.3s;
}
.user-todo {
  font-size: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: row;
}
</style>
