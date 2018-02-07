<template>
  <section class="todoapp">
    <header class="header">
      <h1>Todos</h1>
      <input class="new-todo" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTodo" />
    </header>
    <section class="main" v-show="showTodos">
    <input class="toggle-all" id="toggle-all" type="checkbox" v-model="allDone"/>
      <label for="toggle-all">一键全部完成</label>
      <ul class="todo-list">
       <li v-for="(todo, index) in todos" :key="'todo-' + index" :class="{ completed: todo.completed, editing: todo === editedTodo}">
         <div class="view">
          <input class="toggle" type="checkbox" v-model="todo.completed"/>
          <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
          <button class="destroy"  @click="removeTodo(todo)"></button>
         </div>
         <input class="edit"
          v-model="todo.title"
          v-todo-focus="todo === editedTodo"
          @keyup.enter="doneEdit(todo)"
          @keyup.esc="cancelEdit(todo)"
          @blur="cancelEdit(todo)"
        />
       </li>
     </ul>
    </section>
    <footer class="footer" v-show="showTodos" >
      <span class="todo-count"><strong>{{activeCount}}</strong> 项未完成</span>
      <ul class="filters">
        <li> <a href="#">全部</a> </li>
        <li> <a href="#">未完成</a> </li>
        <li> <a href="#">已完成</a> </li>
      </ul>
      <button class="clear-completed" @click="clearAllTodos">清空已完成</button>
   </footer>
   </section>
</template>

<script>
export default {
  name: 'todoapp',
  data () {
    return {
      newTodo: '', // 存放输入的任务
      todos: [], // 存放所有任务
      editedTodo: null, // 双击任务名称可编辑
      beforeEditCache: '' // 存放双击编辑的字段
    }
  },
  computed: {
    showTodos() {
      return this.todos.length > 0
    },
    activeCount () { // 未完成任务数量计算
      let todoArr = this.todos
      let count = 0
      for (let i = 0; i < todoArr.length; i++) {
					if (todoArr[i].completed === false) {
						count++
					}
				}
				return count
    },
    allDone: {
      get() {
        return this.activeCount === 0
      },
      set(value) {
        this.todos.map(todo => {
          todo.completed = value
        })
      }
    }
  },
  created () {
    let myStorage = window.localStorage.getItem('todos')
    this.todos = JSON.parse(myStorage) || [] // 因为todos初始值是null,使用或运算符，如果为null设为空数组
  },
  methods: {
    addTodo () {
      this.newTodo = this.newTodo.trim()
      if (!this.newTodo) {
        return
      }
      this.todos.unshift({ //
        title: this.newTodo,
        completed: false // 是否完成
      })
      this.newTodo = ''
      window.localStorage.setItem('todos', JSON.stringify(this.todos))
    },
    removeTodo (todo) {
      var index = this.todos.indexOf(todo)
      this.todos.splice(index, 1)
      window.localStorage.setItem('todos', JSON.stringify(this.todos))
    },
    editTodo (todo) {
      this.editedTodo = todo
      this.beforeEditCache = todo.title
      window.localStorage.setItem('todos', JSON.stringify(this.todos))
    },
    doneEdit (todo) {
      if (!this.editedTodo) {
        return;
      }
      this.editedTodo = null;
      todo.title = todo.title.trim()
      if (!todo.title) {
        this.removeTodo(todo)
      }
    },
    cancelEdit (todo) {
      if (this.editedTodo) {
        todo.title = this.beforeEditCache
        this.editedTodo = null
      }
    },
    clearAllTodos () {
      this.todos = this.todos.filter(todo => todo.completed === false)
      window.localStorage.setItem('todos', JSON.stringify(this.todos))
    }
  },
  directives: {
    'todo-focus': {
      update(el) {
        el.focus()
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
