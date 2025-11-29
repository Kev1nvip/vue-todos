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

<style>
/* 移动端重置：清除默认边距、适配触摸元素 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box; /* 关键：边框和内边距不占用额外宽度 */
  -webkit-tap-highlight-color: transparent; /* 清除手机点击元素时的蓝色高亮 */
}

/* 确保页面在小屏幕不横向滚动 */
html, body {
  width: 100%;
  overflow-x: hidden; /* 禁止横向滚动 */
  background-color: #f5f5f5;
}

/* 全局按钮/交互元素：优化手机触摸体验 */
button, input, select, a {
  -webkit-appearance: none; /* 清除 iOS 默认样式 */
  appearance: none;
  outline: none; /* 清除聚焦外边框 */
}

/* 适配 TodoList 容器：小屏幕自动收缩，不溢出 */
.todo-list-wrapper {
  width: 100%;
  max-width: calc(50ch + 150px); /* 保持 50字符+按钮宽度 */
  padding: 0 12px; /* 手机端左右留白，避免紧贴屏幕 */
  margin: 0 auto;
}
</style>