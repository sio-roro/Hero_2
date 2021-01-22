<template>
  <div class="show">
    <input
      type="checkbox"
      v-if="this.userInfo.uid === this.todo.userId"
      v-model="todo.isDone"
      v-on:change="checked(todo)"
      id="box"
    />

    <label class="done-item comment-todo" v-if="todo.isDone" for="box">
      {{ this.todo.item }}
    </label>
    <label v-else for="box" class="comment-todo">{{ this.todo.item }}</label>
    <h2 class="group">
      {{ this.todo.group }} |
      <router-link
        :to="{ path: `/user/${todo.userId}` }"
        v-if="todo.userId != userInfo.uid"
        class="show-userName"
      >
        {{ todo.userName }}</router-link
      >
      <router-link v-else to="/" class="show-userName">
        {{ todo.userName }}</router-link
      >
    </h2>

    <div class="input-area">
      <div class="text-input">
        <input
          type="text"
          v-model="nowComment"
          id="input1"
          :placeholder="placeholder"
        />
        <label for="input1">Todo</label>
      </div>
      <button v-on:click="addComment">Add it</button>
    </div>
    <div class="all-comment">
      <div class="comment-header">
        <div class="comment-header-left">
          <h2>Unread...</h2>
        </div>
        <div class="comment-header-right">
          <h2>Thank you!</h2>
        </div>
      </div>
      <div class="comments">
        <draggable
          class="notYet"
          v-model="comments"
          group="thisGroup"
          :options="options"
          @start="drag = true"
          @end="drag = false"
          @add="onAddY"
        >
          <div
            class="comment"
            v-for="comment in comments"
            :key="comment.id"
            @dragstart="dragList($event, comment)"
          >
            <i class="fas fa-bars"></i>
            <div class="userName">{{ comment.userName }}</div>
            <div class="comment-item">{{ comment.item }}</div>
            <div
              v-if="userInfo.uid === comment.userId"
              v-on:click="deleteTodo(comment, index, comments)"
              class="del-btn"
            >
              <i class="fas fa-times"></i>
            </div>
          </div>
        </draggable>
        <draggable
          class="thank"
          v-model="doneComments"
          group="thisGroup"
          :options="options"
          @start="drag = true"
          @end="drag = false"
          @add="onAddT"
        >
          <div
            class="comment"
            v-for="comment in doneComments"
            :key="comment.id"
            @dragstart="dragList($event, comment)"
          >
            <i class="fas fa-bars"></i>
            <div class="userName">{{ comment.userName }}</div>
            <div class="comment-item">{{ comment.item }}</div>
            <div
              v-if="userInfo.uid === comment.userId"
              v-on:click="deleteTodo(comment, index, doneComments)"
              class="del-btn"
            >
              <i class="fas fa-times"></i>
            </div>
          </div>
        </draggable>
      </div>
    </div>
  </div>
</template>
<script>
import firebase from "firebase";
import draggable from "vuedraggable";
export default {
  name: "show",
  components: { draggable },

  data() {
    return {
      options: {
        group: "myGroup",
        animation: 200,
      },
      todo: [],
      db: null,
      nowComment: "",
      comments: [],
      doneComments: [],
      userInfo: null,
      nowItem: null,
      placeholder: "Type comment to this todo!",
    };
  },
  created() {
    firebase.auth().onAuthStateChanged((user) => {
      this.userInfo = user;
      console.log("User:", this.userInfo.displayName);
    });
    firebase
      .firestore()
      .collection("Comment")
      .where("todoId", "==", this.$route.params["id"])
      .where("thank", "==", false)
      .get()
      .then((comments) => {
        for (const doc of comments.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.comments.push(todo);
          console.log("firebase:", doc.data());
        }
      });
    // ユーザー名を表示してたい

    firebase
      .firestore()
      .collection("Comment")
      .where("todoId", "==", this.$route.params["id"])
      .where("thank", "==", true)
      .get()
      .then((comments) => {
        for (const doc of comments.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.doneComments.push(todo);
          console.log("firebase:", doc.data());
        }
      });
  },
  mounted: function() {
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
    addComment() {
      if (this.nowComment != "") {
        const ref = firebase
          .firestore()
          .collection("Comment")
          .doc();

        ref.set({
          item: this.nowComment,
          id: ref.id,
          todoId: this.todo.id,
          createdAt: new Date(),
          userName: this.userInfo.displayName,
          userId: this.userInfo.uid,
          thank: false,
        });

        this.comments.push({
          item: this.nowComment,
          id: ref.id,
          todoId: this.todo.id,
          createdAt: new Date(),
          userName: this.userInfo.displayName,
          userId: this.userInfo.uid,
          thank: false,
        });
        const number = this.todo.comment;
        firebase
          .firestore()
          .collection("todoA")
          .doc(this.$route.params["id"])
          .update({ comment: number + 1 });
        console.log("added text:", this.comments);

        this.nowComment = "";
        this.placeholder = "(´▽｀)ｱﾘｶﾞﾄ!";
      } else {
        console.log("comment input is empty");
        this.placeholder = "(´・ω・`)";
      }
    },
    deleteTodo(comment, index, group) {
      group.splice(index, 1);

      firebase
        .firestore()
        .collection("Comment")
        .doc(comment.id)
        .delete();
      this.placeholder = "ヽ(･_･、)ノ";
      const number = this.todo.comment;
      firebase
        .firestore()
        .collection("todoA")
        .doc(this.$route.params["id"])
        .update({ comment: number - 1 });
      console.log("added text:", this.comments);
    },
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
    dragList: function(event, listIndex) {
      console.log(listIndex);
      this.nowItem = listIndex;
    },
    onAddY() {
      const ref = firebase
        .firestore()
        .collection("Comment")
        .doc(this.nowItem.id);
      ref.set({ thank: false }, { merge: true });
    },
    onAddT() {
      const ref = firebase
        .firestore()
        .collection("Comment")
        .doc(this.nowItem.id);
      ref.set({ thank: true }, { merge: true });
    },
  },
};
</script>
<style>
.comment-todo {
  color: #fff;
  font-size: 50px;
}
.group {
  color: #fff;
}
body {
  background-color: #484848;
  padding-bottom: 30px;
}
.show {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  width: 100%;
}
.done-item {
  text-decoration: line-through;
  color: #fff;
}
.all-comment {
  width: 100%;
}
.notYet {
  width: 100%;
  border-top: 5px solid #fff;
  border-bottom: 5px solid #fff;
  min-height: 400px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}
.thank {
  width: 100%;
  border-top: 5px solid #42b983;
  border-bottom: 5px solid #42b983;

  min-height: 400px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}
.comment-header {
  display: flex;
  flex-direction: row;
}
.comment-header-left {
  color: #fff;
  width: 50%;
}
.comment-header-right {
  color: #42b983;
  width: 50%;
}
.comments {
  display: flex;
  text-align: center;
  justify-content: center;
  flex-direction: row;
}
/* Input */
@import url(https://fonts.googleapis.com/css?family=Montserrat);

body {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  font-family: Montserrat;
  background: #484848;
}

.text-input {
  position: relative;
  margin-top: 50px;
}
.text-input input[type="text"] {
  display: inline-block;
  width: 500px;
  height: 40px;
  box-sizing: border-box;
  outline: none;
  border: 1px solid lightgray;
  border-radius: 3px 0 0 3px;
  padding: 10px 10px 10px 100px;
  transition: all 0.1s ease-out;
}

.text-input input[type="text"] + label {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  height: 40px;
  line-height: 40px;
  color: white;
  border-radius: 3px 0 0 3px;
  padding: 0 20px;
  background: #42b983;
  transform: translateZ(0) translateX(0);
  transition: all 0.3s ease-in;
  transition-delay: 0.2s;
}

.text-input input[type="text"]:focus + label {
  transform: translateY(-120%) translateX(0%);
  border-radius: 3px;
  transition: all 0.1s ease-out;
}

.text-input input[type="text"]:focus {
  padding: 10px;
  transition: all 0.3s ease-out;
  transition-delay: 0.2s;
}
.input-area {
  display: flex;
  align-items: flex-end;
  justify-content: center;
  flex-direction: row;
  margin-bottom: 40px;
}
button {
  padding: 20px;
  color: white;
  background-color: #42b983;
  font-family: Montserrat;
  height: 40px;
  border: 0px solid #42b983;
  border-radius: 0 3px 3px 0;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0.7;
}
button:hover {
  opacity: 1;
  background-color: #ff8c00;
  transition: all 0.3s;
  border: 0px solid #42b983;
}
/* チェックボックス */
@import url(https://fonts.googleapis.com/css?family=Open+Sans);
input[type="checkbox"] {
  display: none;
}

input[type="checkbox"] + .comment-todo {
  display: block;
  position: relative;
  padding-left: 35px;

  font: 30px/20px "Open Sans";
  color: #fff;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
}

input[type="checkbox"] + .comment-todo:last-child {
  margin-bottom: 0;
}

input[type="checkbox"] + .comment-todo:before {
  content: "";
  display: block;
  width: 20px;
  height: 20px;
  border: 2px solid #42b983;
  position: absolute;
  left: 0;
  top: -2px;
  opacity: 0.8;
  -webkit-transition: all 0.12s, border-color 0.08s;
  transition: all 0.12s, border-color 0.08s;
}

input[type="checkbox"]:checked + .comment-todo:before {
  width: 10px;
  top: -5px;
  left: 5px;
  border-radius: 0;
  opacity: 1;
  border-top-color: transparent;
  border-left-color: transparent;
  -webkit-transform: rotate(45deg);
  transform: rotate(45deg);
}
/* コメントカード */
.comment {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: row;
  margin: 10px;
  padding: 10px;
  border: 1px solid #7f7f7f;
  border-radius: 10px;
  background-color: #dcdcdc;
}
.comment:active {
  cursor: grabbing;
  background-color: #008b8b;
  border: 1px solid #008b8b;
  transition: all 0.5s;
}
.comment:hover {
  cursor: grab;
  background-color: #ffffff;
  transition: all 0.3s;
}
.comment-item {
  padding: 8px;
}
.userName {
  padding: 8px;
  color: #008b8b;
  border-right: 2px solid #000;
}
.fa-bars {
  color: #008b8b;
}
.fa-times {
  color: #dcdcdc;
  font-size: 25px;
  padding-left: 7px;
}
.fa-times:hover {
  color: red;
  transition: all 0.3s;
}
.show-userName {
  text-decoration: none;
  color: #fff;
}
.show-userName:hover {
  color: #008b8b;
  transition: all 0.5s;
}
</style>
