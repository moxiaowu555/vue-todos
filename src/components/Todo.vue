<template>
  <div class="hello">
    <section class="todoapp">
			<header class="header">
				<h1>todos</h1>
				<input class="new-todo" placeholder="What needs to be done?" autofocus @keydown.enter="addTask">
			</header>
			<section class="main">
				<input id="toggle-all" class="toggle-all" type="checkbox" v-model="toggleAll">
				<label for="toggle-all">Mark all as complete</label>
				<ul class="todo-list">
					<li
            :class="{completed: item.completed, editing: item.isEdited}"
            v-for="(item, index) in filteredTodos"
            :key="item.id">
						<div class="view">
							<input
                class="toggle"
                type="checkbox"
                v-model="item.completed"
                @click="changeTaskStatus(item)">
							<label @dblclick="showEditInput(item)">{{item.title}}</label>
							<button class="destroy" @click="deleteTask(item, index)"></button>
						</div>
						<input
              class="edit"
              v-model="editedTodo"
              v-focus
              @keydown.enter="saveTask(item)"
              @blur="saveTask(item)"
              @keydown.27="cancelEdit(item)">
					</li>
				</ul>
			</section>
			<footer class="footer" v-if="todoList.length !== 0">
				<span class="todo-count"><strong>{{ leftCount }}</strong> {{leftCount > 1 ? 'items' : 'item'}} left</span>
				<ul class="filters">
					<li>
						<a :class="{selected: this.visibility === 'all'}" href="javascript:;" @click="showAllTodos">All</a>
					</li>
					<li>
						<a href="javascript:;" :class="{selected: this.visibility === 'active'}" @click="showActiveTodos">Active</a>
					</li>
					<li>
						<a href="javascript:;" :class="{selected: this.visibility === 'completed'}" @click="showCompletedTodos">Completed</a>
					</li>
				</ul>
				<button
          class="clear-completed"
          v-if="!showClearCompleted"
          @click="clearCompleted">Clear completed
        </button>
			</footer>
		</section>
  </div>
</template>

<script>
export default {
  name: 'Todo',
  data () {
    return {
      // 已编辑的文本
      editedTodo: '',
      tempTodos: [],
      todoList: [
        {id: 1, title: '看电影', completed: true, isEdited: false},
        {id: 2, title: '睡觉', completed: true, isEdited: false},
        {id: 3, title: '打篮球', completed: false, isEdited: false}
      ],
      // 未完成任务数量
      leftCount: 1,
      visibility: 'all'
    }
  },
  computed: {
    // 全选和反选功能
    toggleAll: {
      get () {
        // console.log(this.todoList.every(todo => todo.completed)) false
        return this.todoList.every(todo => todo.completed)
      },
      set () {
        // this.toggleAll 为true
        const checked = !this.toggleAll // false
        this.todoList.forEach(item => {
          item.completed = checked
        })
        this.leftCount = checked ? 0 : this.todoList.length
      }
    },
    // 检测是否存在completed任务，来决定clear completed的显示或隐藏
    showClearCompleted: {
      get () {
        return this.todoList.every(todo => !todo.completed)
      }
    },
    filteredTodos () {
      if (this.visibility === 'active') {
        return this.todoList.filter(item => !item.completed)
      } else if (this.visibility === 'completed') {
        return this.todoList.filter(item => item.completed)
      } else {
        return this.todoList
      }
    }
  },
  methods: {
    // 添加任务
    addTask (e) {
      // console.log(e.target.value)
      const target = e.target
      const value = target.value
      // 把获取到的值添加到数组中
      const todos = this.todoList
      if (!value.length) {
        alert('任务不能为空')
        return
      }
      todos.push({
        id: todos.length ? todos[todos.length - 1].id + 1 : 1,
        title: value,
        completed: false,
        isEdit: false
      })
      // 清空文本框的值
      target.value = ''
      this.leftCount++
    },
    // 删除任务
    deleteTask (item, index) {
      this.todoList.splice(index, 1)
      if (!item.completed) this.leftCount--
    },
    // 显示编辑任务框
    showEditInput (item) {
      item.isEdited = !item.isEdited
      this.editedTodo = item.title
    },
    // 保存编辑任务
    saveTask (item) {
      item.title = this.editedTodo
      item.isEdited = false
    },
    // 取消编辑
    cancelEdit (item) {
      item.isEdited = false
    },
    // 切换任务状态
    changeTaskStatus (item) {
      // 注意：这里点击的时候传的 item.completed 是未切换时的值
      this.leftCount = this.leftCount + (item.completed ? 1 : -1)
    },
    // 清空已完成任务
    clearCompleted () {
      // 1. 遍历删除
      // const todos = this.todoList
      // for (var i = todos.length - 1; i >= 0; i--) {
      //   if (todos[i].completed) {
      //     todos.splice(i, 1)
      //   }
      // }
      // 2. 过滤器，返回一个新数组
      var todos = this.todoList.filter(item => !item.completed)
      this.todoList = todos
    },
    // 显示全部任务
    showAllTodos () {
      this.visibility = 'all'
    },
    // 显示未完成任务
    showActiveTodos () {
      this.visibility = 'active'
    },
    showCompletedTodos () {
      this.visibility = 'completed'
    }
  },
  directives: {
    focus: {
      update: function (el, binding) {
        el.focus()
        if (typeof el.selectionStart === 'number') {
          el.selectionStart = el.selectionEnd = el.value.length
        } else if (typeof el.createTextRange !== 'undefined') {
          el.focus()
          var range = el.createTextRange()
          range.collaps(false)
          range.select()
        }
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
