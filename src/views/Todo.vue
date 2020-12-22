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
          <input type="checkbox" v-model="todo.isDone" />
          <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
          <div v-else>{{ todo.item }}</div>
          <div class="todo-id">{{ todo.id }}</div>
          <div v-on:click="deleteTodo(todo, index)" class="del-btn">
            <i class="fas fa-times"></i>
          </div>
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
    };
  },
  methods: {
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
    checked(todo) {
      todo.isDone = "true";
    },
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
  created() {
    firebase
      .firestore()
      .collection("todoA")
      .get()
      .then((collection) => {
        for (const doc of collection.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.todoA.push(todo);
          console.log("firebace:", doc.data());
        }
      });
  },
};
</script>
<style>
h1 {
  color: #e6e6e6;
}
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
