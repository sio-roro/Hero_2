<template>
  <div class="home">
    <div class="add-area">
      <h1>{{ header }}</h1>
      <input type="text" v-model="inputText" />
      <input
        type="button"
        value="add todo"
        v-on:click="addTodo"
        v-on:change="checked(todo)"
      />
    </div>
    <div class="todo-area">
      <div v-for="(todo, index) in todoA" :key="todo.id">
        <div class="todo">
          <input
            type="checkbox"
            v-model="todo.isDone"
            v-on:change="checked(todo)"
          />
          <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
          <div v-else>{{ todo.item }}</div>
          <div class="todo-id">{{ todo.id }}</div>
          <div v-on:click="deleteTodo(todo, index)" class="del-btn">
            <i class="fas fa-times"></i>
          </div>
          <router-link :to="{ path: `/show/${todo.id}` }">
            show
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import firebase from "firebase";
export default {
  name: "Todo",
  components: {},
  data() {
    return {
      todoA: [],
      header: "HERO 2.0",
      inputText: "",
      userInfo: null,
    };
  },
  methods: {
    // 新規のtodoを作成
    addTodo() {
      if (this.inputText != "") {
        const ref = firebase
          .firestore()
          .collection("todoA")
          .doc();
        ref.set({
          item: this.inputText,
          isDone: false,
          id: ref.id,
          userId: this.userInfo.uid,
        });

        this.todoA.push({
          item: this.inputText,
          isDone: false,
          id: ref.id,
        });

        console.log("added text:", this.todoA);

        this.inputText = "";
        this.header = "ADD it!";
      } else {
        this.header = "add me";
      }
    },
    // チェックする
    checked(todo) {
      var which = firebase
        .firestore()
        .collection("todoA")
        .doc(todo.id);

      which.set(
        {
          isDone: todo.isDone,
        },
        {
          merge: true,
        }
      );
    },
    // 削除する
    deleteTodo(todo, index) {
      this.todoA.splice(index, 1);
      console.log("result:", this.todoA);
      firebase
        .firestore()
        .collection("todoA")
        .doc(todo.id)
        .delete();
    },
  },
  // リロード時にデータを取得
  created() {},
  mounted() {
    firebase.auth().onAuthStateChanged((user) => {
      this.isAuth = !!user;
      this.userInfo = user;
      console.log("User:", this.userInfo.displayName);
    });
    var user = firebase.auth().currentUser;
    firebase
      .firestore()
      .collection("todoA")
      .where("userId", "==", user.uid)
      .get()
      .then((collection) => {
        for (const doc of collection.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.todoA.push(todo);
          console.log("firebase:", doc.data());
        }
      });
  },
};
</script>
<style>
.done-item {
  text-decoration: line-through;
}
.fa-times:hover {
  color: red;
}
.todo {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}
</style>
