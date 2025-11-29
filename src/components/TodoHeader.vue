<template>
  <div class="hdContainer">
    <h1 class="hdTitle">待办事项</h1>
    <input 
      class="newTodo"
      autofocus
      placeholder="请输入您的待办事项，按下回车后即可添加"
      v-model.trim="newTodo"
      @keyup.enter="addNewTodo"
    />
    <p v-show="isShowMsg" class="hdMsg">
      最多输入{{ countLimit }}个字符!!!
    </p>
  </div>
</template>

<script>
const WORD_COUNT_LIMIT = 50;
export default{
  data(){
    return {
      newTodo:"",
      countLimit:WORD_COUNT_LIMIT
    }
  },
  computed:{
    isShowMsg(){
      return this.newTodo.length > WORD_COUNT_LIMIT;
    }
  },
  methods:{
    addNewTodo(){
      if(this.newTodo.length == 0 || this.newTodo.length > WORD_COUNT_LIMIT){
        return ;
      }
      this.$emit("addTodo", this.newTodo);
      this.newTodo = "";
    }
  }
}
</script>

<style scoped>
.hdContainer {
  text-align: center;
  font-size: 16px;
  padding: 0 12px;
  /* 新增容器内边距统一管理 */
  box-sizing: border-box;
}

.hdTitle {
  color: #4e6ef2;
  margin: 16px 0;
}

.newTodo {
  width: 100%;
  padding: 14px 16px;
  border: 1px solid #eee;
  border-radius: 10px;
  font-size: 15px;
  box-sizing: border-box; /* 确保padding不影响宽度计算 */
  max-width: 500px;
  margin: 0 auto;
  display: block; /* 确保输入框作为块级元素保持居中 */
}

.newTodo:focus {
  outline-color: #4e6ef2;
  border-color: #4e6ef2;
}

.hdMsg {
  color: red;
  margin: 8px auto 0; /* 上下间距优化，水平居中 */
  font-size: 13px;
  text-align: left;
  /* 关键修改：与输入框保持相同的最大宽度和左右边距 */
  max-width: 500px;
  padding: 0 16px; /* 与输入框的左右padding保持一致 */
  box-sizing: border-box; /* 确保padding不影响宽度计算 */
}
</style>