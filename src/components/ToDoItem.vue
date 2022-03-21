<template>
    <div class="todo-item">
        <div class="todo-item-left">
            <input type="checkbox" v-model="completed" @change="doneEdit">
            <div v-if="!edit" @dblclick="editToDo" class="todo-item-label" :class="{ completed : completed }">
            {{ task }}
            <br><span class="date-time">{{ timestamp }}</span>
            </div>
            <input v-else class="todo-item-edit" type="text" v-model="task" @blur="doneEdit" @keyup.enter="doneEdit" @keyup.esc="cancelEdit">
        </div>
        <div class="remove-item" @click="removeToDo(toDo.id)">
            &times;
        </div>
    </div>
</template>

<script>
export default {
  name: 'toDo-item',
  props: {
    toDo: {
      type: Object,
      required: true
    },
    checkAll: {
      type: Boolean,
      required: true
    }
  },
  data () {
    return {
      'id': this.toDo.id,
      'task': this.toDo.task,
      'completed': this.toDo.completed,
      'edit': this.toDo.editing,
      'beforeEditCache': '',
      'timestamp': this.toDo.timestamp
    }
  },
  watch: {
    checkAll () {
      if (this.checkAll) {
        this.completed = true
      } else {
        this.completed = this.toDo.completed
      }
    }
  },
  directives: {
    inserted: function (el) {
      el.focus()
    }
  },
  methods: {
    removeToDo (id) {
      this.$emit('removedToDo', id)
    },
    editToDo () {
      this.beforeEditCache = this.task
      this.edit = true
    },
    doneEdit () {
      if (this.task.trim() === '') {
        this.task = this.beforeEditCache
      }
      this.edit = false
      this.$emit('finishedEdit', {
        'id': this.id,
        'task': this.task,
        'completed': this.completed,
        'edit': this.edit
      })
    },
    cancelEdit () {
      this.task = this.beforeEditCache
      this.edit = false
    },
    currentDate: function () {
      return new Date().toLocaleDateString()
    },
    currentTime: function () {
      return new Date().toLocaleTimeString()
    }
  },
  mounted: function () {
    this.timestamp = this.currentDate() + ' ' + this.currentTime()
  }
}
</script>
