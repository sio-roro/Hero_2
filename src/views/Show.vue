<template>
  <div class="show">
    <h1>this is show page</h1>
    <h1>{{ this.$route.params["id"] }}</h1>
    <input type="checkbox" v-model="todo.isDone" v-on:change="checked(todo)" />
    <div class="done-item" v-if="todo.isDone">{{ this.todo.item }}</div>
    <div v-else>{{ this.todo.item }}</div>
    <div class="group">{{ this.todo.group }}</div>
  </div>
</template>
<script>
import firebase from "firebase";

export default {
  name: "show",
  components: {},
  data() {
    return {
      todo: [],
      db: null,
    };
  },
  created() {
    this.db = firebase.firestore();
  },
  mounted() {
    firebase
      .firestore()
      .collection("todoA")
      .doc(this.$route.params["id"])
      .get()
      .then((collection) => {
        console.log("This Todo:", collection.data());
        this.todo = collection.data();
      });
  },
  methods: {
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
      console.log("Checked:", todo.isDone);
    },
  },
};
</script>
<style>
.done-item {
  text-decoration: line-through;
}
</style>
