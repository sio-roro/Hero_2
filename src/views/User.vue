<template>
  <div class="user">
    <h1>usernPage</h1>
    <h1>{{ nowUser.userName }}</h1>
    <div class="user_todoA">
      <div>groupA</div>
      <div v-for="todo in userTodoA" :key="todo.id">
        <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
        <div v-else>{{ todo.item }}</div>
      </div>
    </div>
    <div class="user_todoB">
      <div>groupB</div>
      <div v-for="todo in userTodoB" :key="todo.id">
        <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
        <div v-else>{{ todo.item }}</div>
      </div>
    </div>
    <div class="user_todoC">
      <div>groupC</div>
      <div v-for="todo in userTodoC" :key="todo.id">
        <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
        <div v-else>{{ todo.item }}</div>
      </div>
    </div>
    <div class="user_todoD">
      <div>groupD</div>
      <div v-for="todo in userTodoD" :key="todo.id">
        <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
        <div v-else>{{ todo.item }}</div>
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
      userTodoA: [],
      userTodoB: [],
      userTodoC: [],
      userTodoD: [],
    };
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
};
</script>
<style>
#app {
  background-color: #484848;
}
.user {
  background-color: #484848;
}
.user_todoA {
  height: 100px;
  border: 1px soild #000;
}
.user_todoB {
  height: 100px;
  border: 1px solid #008b8b;
}
.user_todoC {
  height: 100px;
  border: 1px solid #ff8c00;
}
.user_todoD {
  height: 100px;
  border: 1px solid #ff1493;
}
.done-item {
  text-decoration: line-through;
}
</style>
