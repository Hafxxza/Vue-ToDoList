<template>
  <div>
    <div>
      <h1 class="title">Hafeeza's To Do List</h1>
    </div>
    <input type="text" class="todo-input" placeholder="What do you wanna do today?" v-model="newToDo" @keyup.enter="addToDo">
    <toDo-item v-for="toDo in toDosFiltered" :key="toDo.id" :toDo="toDo" :checkAll="!anyRemaining" @removedToDo="removeToDo" @finishedEdit="finishedEdit">
    </toDo-item>
    <div class="extra-container">
      <div>
        <label>
          <input type="checkbox" :checked="!anyRemaining" @change="checkAllToDos">
          Mark all as completed
        </label>
      </div>
      <div>
        {{ remaining }} task(s) left
      </div>
    </div>

    <div class="extra-container">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
        <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Active</button>
        <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
      </div>
      <div>
        <transition name="fade">
          <button v-if='showClearCompletedButton' @click="clearCompleted">Clear Completed</button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import ToDoItem from './ToDoItem'

export default {
  name: 'Home',
  components: {
    ToDoItem
  },
  data () {
    return {
      newToDo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      toDos: []
    }
  },
  mounted () {
    fetch('http://localhost:3000/toDoList')
      .then(res => res.json())
      .then(data => (this.toDos = data))
      .catch(err => console.log(err.message))
  },
  computed: {
    remaining () {
      return this.toDos.filter(toDo => !toDo.completed).length
    },
    anyRemaining () {
      return this.remaining !== 0
    },
    toDosFiltered () {
      if (this.filter === 'all') {
        return this.toDos
      } else if (this.filter === 'active') {
        return this.toDos.filter(toDo => !toDo.completed)
      } else if (this.filter === 'completed') {
        return this.toDos.filter(toDo => toDo.completed)
      }

      return this.toDos
    },
    showClearCompletedButton () {
      return this.toDos.filter(todo => todo.completed).length > 0
    }
  },
  methods: {
    addToDo () {
      if (this.newToDo.trim() === '') {
        return
      }

      this.toDos.push({
        id: this.idForTodo,
        task: this.newToDo,
        completed: false,
        edit: false
      })

      this.newToDo = ''
      this.idForTodo++
    },
    removeToDo (id) {
      const index = this.toDos.findIndex(item => item.id === id)
      this.toDos.splice(index, 1)
    },
    checkAllToDos () {
      this.toDos.forEach(toDo => (toDo.completed = event.target.checked))
    },
    clearCompleted () {
      this.toDos = this.toDos.filter(toDo => (!toDo.completed))
    },
    finishedEdit (data) {
      const index = this.toDos.findIndex(item => item.id === data.id)
      this.toDos.splice(index, 1, data)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.title {
  text-align: center;
}

.todo-input {
  width: 100%;
  padding: 10px;
  font-size: 16px;
  margin-bottom: 20px;
  border-radius: 5px;
}

.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.remove-item {
  margin-left: 14px;
  cursor: pointer;
}

.todo-item-left {
  display: flex;
  align-items: center;
}

.todo-item-label {
  padding: 10px;
  margin-left: 12px;
}

.todo-item-edit {
  color: darkgreen;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid green;
}

.completed {
  text-decoration: line-through;
  color: grey;
}

.extra-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-bottom: 14px;
}

button {
  font-size: 16px;
  background-color: white;
  appearance: none;
  border-radius: 5px;
}

.active {
  background: lightgreen;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.2s;
}

.fade.enter, .fade-leave-to {
  opacity: 0;
}

.date-time {
  font-size: 14px;
  color: lightslategray;
}
</style>
