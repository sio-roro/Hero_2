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
      <draggable
        tag="ul"
        class="todoA"
        v-model="todoA"
        group="myGroup"
        @start="drag = true"
        @end="drag = false"
        @add="onAddA"
      >
        <div
          v-for="(todo, index) in todoA"
          draggable
          :key="todo.id"
          @dragstart="dragList($event, todo)"
        >
          <div class="todo">
            <input
              type="checkbox"
              v-model="todo.isDone"
              v-on:change="checked(todo)"
            />
            <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
            <div v-else>{{ todo.item }}</div>
            <div class="todo-id">{{ todo.id }}</div>
            <div v-on:click="deleteTodo(todo, index, todoA)" class="del-btn">
              <i class="fas fa-times"></i>
            </div>
            <router-link :to="{ path: `/show/${todo.id}` }">
              show
            </router-link>
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
            <input
              type="checkbox"
              v-model="todo.isDone"
              v-on:change="checked(todo)"
            />
            <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
            <div v-else>{{ todo.item }}</div>
            <div class="todo-id">{{ todo.id }}</div>
            <div v-on:click="deleteTodo(todo, index, todoB)" class="del-btn">
              <i class="fas fa-times"></i>
            </div>
            <router-link :to="{ path: `/show/${todo.id}` }">
              show
            </router-link>
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
            <input
              type="checkbox"
              v-model="todo.isDone"
              v-on:change="checked(todo)"
            />
            <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
            <div v-else>{{ todo.item }}</div>
            <div class="todo-id">{{ todo.id }}</div>
            <div v-on:click="deleteTodo(todo, index, todoC)" class="del-btn">
              <i class="fas fa-times"></i>
            </div>
            <router-link :to="{ path: `/show/${todo.id}` }">
              show
            </router-link>
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
            <input
              type="checkbox"
              v-model="todo.isDone"
              v-on:change="checked(todo)"
            />
            <div class="done-item" v-if="todo.isDone">{{ todo.item }}</div>
            <div v-else>{{ todo.item }}</div>
            <div class="todo-id">{{ todo.id }}</div>
            <div v-on:click="deleteTodo(todo, index, todoD)" class="del-btn">
              <i class="fas fa-times"></i>
            </div>
            <router-link :to="{ path: `/show/${todo.id}` }">
              show
            </router-link>
          </div>
        </div>
      </draggable>
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
      todoA: [],
      todoB: [],
      todoC: [],
      todoD: [],
      nowItem: null,
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
          group: "todoA",
        });

        this.todoA.push({
          item: this.inputText,
          isDone: false,
          id: ref.id,
          userId: this.userInfo.uid,
          group: "todoA",
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
    deleteTodo(todo, index, group) {
      group.splice(index, 1);
      console.log("result:", this.todoA);
      firebase
        .firestore()
        .collection("todoA")
        .doc(todo.id)
        .delete();
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
    },
    onAddB: function(e) {
      console.log("onAdd", e);
      console.log("現在", this.nowItem);
      const ref = firebase
        .firestore()
        .collection("todoA")
        .doc(this.nowItem.id);
      ref.set({ group: "todoB" }, { merge: true });
    },
    onAddC: function(e) {
      console.log("onAdd", e);
      console.log("現在", this.nowItem);
      const ref = firebase
        .firestore()
        .collection("todoA")
        .doc(this.nowItem.id);
      ref.set({ group: "todoC" }, { merge: true });
    },
    onAddD: function(e) {
      console.log("onAdd", e);
      console.log("現在", this.nowItem);
      const ref = firebase
        .firestore()
        .collection("todoA")
        .doc(this.nowItem.id);
      ref.set({ group: "todoD" }, { merge: true });
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
.todoA {
  border: 1px solid #000;
  height: 100px;
  margin: 0%;
}
.todoB {
  border: 1px solid #008b8b;
  height: 100px;
  margin: 0%;
}
.todoC {
  border: 1px solid #ff8c00;
  height: 100px;
  margin: 0%;
}
.todoD {
  border: 1px solid #ff1493;
  height: 100px;
  margin: 0%;
}
</style>
