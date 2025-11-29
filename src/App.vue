<script>
import TodoHeader from './components/TodoHeader.vue'
import TodoList from './components/TodoList.vue'
import TodoFooter from './components/TodoFooter.vue';

export default {
  components:{
    TodoHeader,
    TodoList,
    TodoFooter
  },
  data(){
    return {
      todos:JSON.parse(localStorage.getItem('vue-todos') || '[]'),
      tabType:0,
      priorityOptions: [
        { value: 'high', label: '高', color: '#ff4d4f' },
        { value: 'medium', label: '中', color: '#faad14' },
        { value: 'low', label: '低', color: '#52c41a' }
      ]
    }
  },
  watch:{
    todos:{
      handler(todos){
        localStorage.setItem('vue-todos', JSON.stringify(todos));
      },
      deep:true
    }
  },
  methods:{
    addTodo(newTodo){
      console.log("新增加的任务信息为：" + newTodo);
      this.todos.push({
        id: Date.now(), 
        txt: newTodo,
        completed: false,
        isTop: false,
        priority: 'medium' 
      })
    },
    delTodo(delTodo){
      console.log("要删除的任务id为" + delTodo.id);
      this.todos = this.todos.filter(item => item.id !== delTodo.id);
    },
    changeTabType(type){
      this.tabType = type;
    },
    toggleTop(item){
      const todo = this.todos.find(todo => todo.id === item.id);
      if (todo) todo.isTop = !todo.isTop;
    },
    changePriority(todoId, priority){
      const todo = this.todos.find(item => item.id === todoId);
      if (todo) todo.priority = priority;
    },
    formatTime(timestamp) {
      const date = new Date(timestamp);
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, '0');
      const day = String(date.getDate()).padStart(2, '0');
      const hour = String(date.getHours()).padStart(2, '0');
      const minute = String(date.getMinutes()).padStart(2, '0');
      return `${year}-${month}-${day} ${hour}:${minute}`;
    },
    getPriorityColor(priority){
      const option = this.priorityOptions.find(item => item.value === priority);
      return option ? option.color : '#faad14'; 
    }
  },
  computed: {
    todoList() {
      let filteredTodos;
      if (this.tabType == 0) {
        filteredTodos = [...this.todos];
      } else {
        const tabType = this.tabType;
        filteredTodos = this.todos.filter(item => {
          return tabType == 1 ? !item.completed : item.completed;
        });
      }

      return filteredTodos.sort((a, b) => {
        if (a.isTop && !b.isTop) return -1;
        if (!a.isTop && b.isTop) return 1;
        
        const priorityOrder = { high: 3, medium: 2, low: 1 };
        return priorityOrder[b.priority] - priorityOrder[a.priority];
      });
    }
  },
}
</script>

<template>
  <TodoHeader @addTodo="addTodo"></TodoHeader>
  <TodoList 
    :todos="todoList" 
    :formatTime="formatTime"
    :priorityOptions="priorityOptions"
    :getPriorityColor="getPriorityColor"
    @delTodo="delTodo"
    @toggleTop="toggleTop"
    @changePriority="changePriority"
  ></TodoList>
  <TodoFooter :tabType="tabType" :count="todoList.length" @changeTabType="changeTabType"></TodoFooter>
</template>

<style >
html,
body {
  background-color: #f5f5f5;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.container {
  max-width: 980px; 
  min-height: 100%;
  margin: 0 auto; 
  padding: 0 10px; 
}
</style>