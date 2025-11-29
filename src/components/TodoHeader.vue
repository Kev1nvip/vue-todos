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
}

.hdTitle {
  color: #4e6ef2;
}

.newTodo {
  width: 100%;
  padding: 20px 20px;
  border: none;
  border-radius: 10px;
  font-size: 16px;
  box-sizing: border-box;
  min-width: 400px;
}

.newTodo:focus {
  outline-color: #4e6ef2;
}

.hdMsg {
  color: red;
  margin: 10px 0;
}
</style>