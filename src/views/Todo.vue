<template>
  <div class="home">
    <div class="add-area">
      <h1 v-if="drag" class="display">{{ nowItem.item }}</h1>
      <h1 v-else class="display">{{ header }}</h1>
      <div
        class="indicator"
        v-if="drag"
        :class="{ inMove: drag || coution }"
      ></div>
      <div class="indicator" v-else :class="{ inMove: drag || coution }"></div>
      <div class="input-area">
        <div class="text-input">
          <input
            type="text"
            v-model="inputText"
            id="input1"
            placeholder="Type what to do today in here!"
          />
          <label for="input1">Todo</label>
        </div>
        <button v-on:click="addTodo">Add it</button>
      </div>
    </div>
    <div class="todo_contaner">
      <div class="todo-area">
        <draggable
          tag="ul"
          class="todoA"
          v-model="todoA"
          group="myGroup"
          :options="options"
          @start="drag = true"
          @end="drag = false"
          @add="onAddA"
        >
          <div
            v-for="(todo, index) in todoA"
            :key="todo.id"
            @dragstart="dragList($event, todo)"
          >
            <div class="todo">
              <i class="fas fa-bars"></i>
              <input
                type="checkbox"
                v-model="todo.isDone"
                v-on:change="checked(todo)"
                v-bind:id="'box-1' + index"
              />
              <label
                class="done-item"
                v-if="todo.isDone"
                v-bind:for="'box-1' + index"
                >{{ todo.item }}</label
              >
              <label v-else v-bind:for="'box-1' + index">{{ todo.item }}</label>
              <router-link :to="{ path: `/show/${todo.id}` }">
                <i class="fas fa-comment"></i>
              </router-link>
              <div v-on:click="deleteTodo(todo, index, todoA)" class="del-btn">
                <i class="fas fa-times"></i>
              </div>
            </div>
          </div>
        </draggable>
      </div>
      <div class="todo-area">
        <draggable
          tag="ul"
          class="todoB"
          v-model="todoB"
          group="myGroup"
          :options="options"
          @start="drag = true"
          @end="drag = false"
          @add="onAddB"
        >
          <div
            v-for="(todo, index) in todoB"
            :key="todo.id"
            @dragstart="dragList($event, todo)"
          >
            <div class="todo">
              <i class="fas fa-bars"></i>
              <input
                type="checkbox"
                v-model="todo.isDone"
                v-on:change="checked(todo)"
                v-bind:id="'box-2' + index"
              />
              <label
                class="done-item"
                v-if="todo.isDone"
                v-bind:for="'box-2' + index"
                >{{ todo.item }}</label
              >
              <label v-else v-bind:for="'box-2' + index">{{ todo.item }}</label>
              <router-link :to="{ path: `/show/${todo.id}` }">
                <i class="fas fa-comment"></i>
              </router-link>
              <div v-on:click="deleteTodo(todo, index, todoB)" class="del-btn">
                <i class="fas fa-times"></i>
              </div>
            </div>
          </div>
        </draggable>
      </div>
      <div class="todo-area">
        <draggable
          tag="ul"
          class="todoC"
          v-model="todoC"
          group="myGroup"
          :options="options"
          @start="drag = true"
          @end="drag = false"
          @add="onAddC"
        >
          <div
            v-for="(todo, index) in todoC"
            :key="todo.id"
            @dragstart="dragList($event, todo)"
          >
            <div class="todo">
              <i class="fas fa-bars"></i>
              <input
                type="checkbox"
                v-model="todo.isDone"
                v-on:change="checked(todo)"
                v-bind:id="'box-3' + index"
              />
              <label
                class="done-item"
                v-if="todo.isDone"
                v-bind:for="'box-3' + index"
                >{{ todo.item }}</label
              >
              <label v-else v-bind:for="'box-3' + index">{{ todo.item }}</label>
              <router-link :to="{ path: `/show/${todo.id}` }">
                <i class="fas fa-comment"></i>
              </router-link>
              <div v-on:click="deleteTodo(todo, index, todoC)" class="del-btn">
                <i class="fas fa-times"></i>
              </div>
            </div>
          </div>
        </draggable>
      </div>
      <div class="todo-area">
        <draggable
          tag="ul"
          class="todoD"
          v-model="todoD"
          group="myGroup"
          :options="options"
          @start="drag = true"
          @end="drag = false"
          @add="onAddD"
        >
          <div
            v-for="(todo, index) in todoD"
            :key="todo.id"
            @dragstart="dragList($event, todo)"
          >
            <div class="todo">
              <i class="fas fa-bars"></i>
              <input
                type="checkbox"
                v-model="todo.isDone"
                v-on:change="checked(todo)"
                v-bind:id="'box-4' + index"
              />
              <label
                class="done-item"
                v-if="todo.isDone"
                v-bind:for="'box-4' + index"
                >{{ todo.item }}</label
              >
              <label v-else v-bind:for="'box-4' + index">{{ todo.item }}</label>
              <router-link :to="{ path: `/show/${todo.id}` }">
                <i class="fas fa-comment"></i>
              </router-link>
              <div v-on:click="deleteTodo(todo, index, todoD)" class="del-btn">
                <i class="fas fa-times"></i>
              </div>
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
  name: "Todo",
  components: { draggable },

  data() {
    return {
      options: {
        group: "myGroup",
        animation: 200,
      },
      todoA: [],
      todoB: [],
      todoC: [],
      todoD: [],
      nowItem: null,
      header: "HERO 2.0",
      inputText: "",
      userInfo: null,
      drag: false,
      coution: false,
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
          group: "todoA",
          userName: this.userInfo.displayName,
        });

        this.todoA.push({
          item: this.inputText,
          isDone: false,
          id: ref.id,
          userId: this.userInfo.uid,
          group: "todoA",
          userName: this.userInfo.displayName,
        });

        console.log("added text:", this.todoA);

        this.inputText = "";
        this.header = "(`Д´)ゞﾗｼﾞｬｰ!!";
      } else {
        this.header = "…φ(。。*)ｲｼﾞｲｼﾞ";
        this.coution = true;
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
      if (todo.isDone) {
        this.header = "（　･`ー･´） + ｷﾘｯ";
      } else {
        this.header = "_φ［・ω・｀*］ﾒﾓﾒﾓ♪";
      }
    },
    // 削除する
    deleteTodo(todo, index, group) {
      group.splice(index, 1);
      console.log("result:", this.todoA);
      firebase
        .firestore()
        .collection("todoA")
        .doc(todo.id)
        .delete();
      this.header = "(´∀｀*)ﾉｼ　ﾊﾞｲﾊﾞｲ";
    },
    dragList: function(event, listIndex) {
      console.log(listIndex);
      this.nowItem = listIndex;
    },
    onAddA: function(e) {
      console.log("onAdd", e);
      console.log("現在", this.nowItem);
      const ref = firebase
        .firestore()
        .collection("todoA")
        .doc(this.nowItem.id);
      ref.set({ group: "todoA" }, { merge: true });
      this.header = "HERO2.0";
      this.coution = false;
    },
    onAddB: function(e) {
      console.log("onAdd", e);
      console.log("現在", this.nowItem);
      const ref = firebase
        .firestore()
        .collection("todoA")
        .doc(this.nowItem.id);
      ref.set({ group: "todoB" }, { merge: true });
      this.header = "HERO2.0";
      this.coution = false;
    },
    onAddC: function(e) {
      console.log("onAdd", e);
      console.log("現在", this.nowItem);
      const ref = firebase
        .firestore()
        .collection("todoA")
        .doc(this.nowItem.id);
      ref.set({ group: "todoC" }, { merge: true });
      this.header = "HERO2.0";
      this.coution = false;
    },
    onAddD: function(e) {
      console.log("onAdd", e);
      console.log("現在", this.nowItem);
      const ref = firebase
        .firestore()
        .collection("todoA")
        .doc(this.nowItem.id);
      ref.set({ group: "todoD" }, { merge: true });
      this.header = "HERO2.0";
      this.coution = false;
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
      .where("group", "==", "todoA")
      .get()
      .then((collection) => {
        for (const doc of collection.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.todoA.push(todo);
          console.log("firebase:", doc.data());
        }
      });
    firebase
      .firestore()
      .collection("todoA")
      .where("userId", "==", user.uid)
      .where("group", "==", "todoB")
      .get()
      .then((collection) => {
        for (const doc of collection.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.todoB.push(todo);
          console.log("firebase:", doc.data());
        }
      });
    firebase
      .firestore()
      .collection("todoA")
      .where("userId", "==", user.uid)
      .where("group", "==", "todoC")
      .get()
      .then((collection) => {
        for (const doc of collection.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.todoC.push(todo);
          console.log("firebase:", doc.data());
        }
      });
    firebase
      .firestore()
      .collection("todoA")
      .where("userId", "==", user.uid)
      .where("group", "==", "todoD")
      .get()
      .then((collection) => {
        for (const doc of collection.docs) {
          const todo = doc.data(); // { createdAt: "xxx", title: "asdfasdf" }
          this.todoD.push(todo);
          console.log("firebase:", doc.data());
        }
      });
  },
};
</script>
<style>
.home {
  width: 100%;
}
body {
  background-color: #484848;
  padding-bottom: 30px;
}
.done-item {
  text-decoration: line-through;
  opacity: 0.5;
}
.input-area {
  padding-top: 0px;
}
.display {
  color: whitesmoke;
}
.inMove {
  background: #ff8c00 !important;
}
.indicator {
  margin: auto;
  width: 300px;
  height: 30px;
  border-radius: 3px;
  background: #42b983;
}
.todo {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}
.todoA {
  border-bottom: 1px solid #008b8b;
  border-right: 1px solid #008b8b;
  min-height: 250px;
  margin: 0%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}
.todoB {
  border-bottom: 1px solid #008b8b;
  border-left: 1px solid #008b8b;

  min-height: 250px;
  margin: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}
.todoC {
  border-top: 1px solid #008b8b;
  border-right: 1px solid #008b8b;

  min-height: 250px;
  margin: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}
.todoD {
  border-top: 1px solid#008b8b;
  border-left: 1px solid #008b8b;

  min-height: 250px;
  margin: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}
.todo_contaner {
  width: 100%;

  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}
.todo-area {
  width: 50%;
  height: 50%;
}

/* チェックボックス */
@import url(https://fonts.googleapis.com/css?family=Open+Sans);
input[type="checkbox"] {
  display: none;
}

input[type="checkbox"] + label {
  display: block;
  position: relative;
  padding-left: 35px;

  font: 14px/20px "Open Sans", Arial, sans-serif;
  color: black;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
}

input[type="checkbox"] + label:last-child {
  margin-bottom: 0;
}

input[type="checkbox"] + label:before {
  content: "";
  display: block;
  width: 20px;
  height: 20px;
  border: 2px solid #008b8b;
  position: absolute;
  left: 0;
  top: -2px;
  opacity: 0.8;
  -webkit-transition: all 0.12s, border-color 0.08s;
  transition: all 0.12s, border-color 0.08s;
}

input[type="checkbox"]:checked + label:before {
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
.todo {
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
.todo:active {
  cursor: grabbing;
  background-color: #008b8b;
  border: 1px solid #008b8b;
  transition: all 0.5s;
}
.todo:hover {
  cursor: grab;
  background-color: #ffffff;
  transition: all 0.3s;
}
.fa-times {
  color: #dcdcdc;
  font-size: 25px;
  padding-left: 7px;
}
.fa-comment {
  color: #dcdcdc;
  font-size: 25px;
  padding-left: 7px;
}
.fa-times:hover {
  color: red;
  transition: all 0.3s;
}
.fa-comment:hover {
  color: #008b80;
  transition: all 0.3s;
}
.fa-bars {
  font-size: 20px;
  padding-right: 7px;
  color: #008b8b;
}
/* text-area */
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
.comment-todo {
  color: #fff;
  font-size: 50px;
}
</style>
