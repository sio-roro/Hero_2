<template>
  <div class="show">
    <h1>this is show page</h1>
    <h1>{{ this.$route.params["id"] }}</h1>
    <h1>{{ this.todo.item }}</h1>
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
};
</script>
