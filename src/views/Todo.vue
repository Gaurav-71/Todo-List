<template>
  <div class="todo">
    <div class="todo-list">
      <form @submit.prevent="addTodo">
        <input type="text" placeholder="+ Enter a new task" v-model="tempTodo" />
      </form>
      <div class="loading-icon" v-if="$store.state.isTodosLoading">
        <img src="../assets/loading.svg" alt="loading" style="width: 80px" />
        <p>Fetching your data</p>
      </div>
      <div class="tasks">
        <ul>
          <li v-for="todo in getList" :key="todo.id">
            <div v-if="!(todo.id == currentEdit)" class="todo-container">
              <div>{{ todo.Task }}</div>
              <div class="buttons">
                <button @click="editTodo(todo)">
                  <img src="../assets/edit.svg" alt="edit" />
                </button>
                <button @click="deleteTodo(todo.id)">
                  <img src="../assets/delete.svg" alt="todo-logo" />
                </button>
              </div>
            </div>
            <div v-else class="todo-container">
              <input type="text" v-model="todo.Task" />
              <div class="buttons">
                <button @click="saveEdit(todo)">
                  <img src="../assets/save.svg" alt="todo-logo" />
                </button>
                <button @click="cancelEdit(todo)">
                  <img src="../assets/cancel.svg" alt="edit" />
                </button>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  name: "Todo",
  data() {
    return {
      tempTodo: "",
      unsubscribe: null,
      currentEdit: null,
      currentEditValue: ""
    };
  },
  methods: {
    addTodo() {
      let task = this.tempTodo.trim();
      if (task.length != 0) {
        this.$store
          .dispatch("addTodo", {
            Task: task,
            timestamp: Date.now(),
            activeUser: this.$store.state.user.uid,
            isEditing: false
          })
          .then(() => {})
          .catch(err => {
            console.log(err);
          });
      }
      this.tempTodo = "";
    },
    deleteTodo(id) {
      this.$store
        .dispatch("deleteTodo", id)
        .then(resp => {
          console.log(resp);
        })
        .catch(err => {
          alert(err);
        });
    },
    editTodo(obj) {
      this.currentEdit = obj.id;
      this.currentEditValue = obj.Task;
    },
    saveEdit(obj) {
      this.$store
        .dispatch("saveEdit", obj)
        .then(resp => {
          console.log(resp);
        })
        .catch(err => {
          console.log(err);
        });
      this.currentEdit = null;
    },
    cancelEdit(obj) {
      this.currentEdit = null;
      obj.Task = this.currentEditValue;
    }
  },
  computed: {
    ...mapGetters(["getList"])
  },
  mounted() {
    this.$store
      .dispatch("loadTodos")
      .then(resp => {
        this.unsubscribe = resp;
      })
      .catch(err => {
        alert(err);
      });
  },
  beforeDestroy() {
    this.unsubscribe();
  }
};
</script>

<style scoped lang="scss">
.todo {
  border-radius: 1em;
  background-color: none;
  .todo-list {
    form {
      width: 100%;
      input {
        width: calc(100% - 40px);
        border-radius: 1em;
        background-color: rgb(15, 14, 14);
        padding: 20px;
        font-size: 1.3em;
        border: 2px solid grey;
        border-radius: 1em;
        box-shadow: 0 0 20px black;
        outline: none;
        color: grey;
      }
    }
    .loading-icon {
      margin-top: 1em;
      text-align: center;
      p {
        margin-top: 0;
        color: rgb(211, 50, 50);
        font-size: 1.5rem;
      }
    }
    ul {
      margin: 0;
      padding: 0;
      margin-top: 30px;
      list-style-type: none;
      color: orangered;
      border: 2px solid grey;
      border-radius: 1em;
      box-shadow: 0 0 20px black;
      li {
        text-align: left;
        padding: 20px;
        font-size: 1.3em;
        border-bottom: 1px solid grey;
        word-wrap: break-word;
        .todo-container {
          display: grid;
          align-items: center;
          grid-template-columns: 90% 10%;
          input {
            border: none;
            background: none;
            text-align: left;
            font-size: 1.2rem;
            color: grey;
            outline: none;
          }
          .buttons {
            text-align: center;
            button {
              cursor: pointer;
              background: none;
              border: none;
              outline: none;
              img {
                width: 27px;
                outline: none;
              }
            }
          }
        }
      }
    }
  }
}
</style>
