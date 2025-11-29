<template>
  <div class="tdContainer">
    <ul class="tdList">
      <li class="tdItem" v-for="item in todos" :key="item.id">
        <div class="tdItem-main">
          <input type="checkbox" v-model="item.completed" class="tdToggle" />
          <div class="tdContent">
            <div class="tdTxt-wrap">
              <span class="priority-tag" :style="{ backgroundColor: getPriorityColor(item.priority) }"></span>
              <span class="tdTxt" :class="{ completed: item.completed }">
                {{ item.txt }}
                <span class="top-tag" v-if="item.isTop">置顶</span>
              </span>
            </div>
            <span class="tdCreateTime">{{ formatTime(item.id) }}</span>
          </div>
        </div>

        <div class="tdItem-acts">
          <select class="priority-select" :value="item.priority"
            @change="handlePriorityChange(item.id, $event.target.value)">
            <option v-for="opt in priorityOptions" :key="opt.value" :value="opt.value">
              {{ opt.label }}
            </option>
          </select>
          <a @click.stop="toggleTop(item)" class="top-btn">
            {{ item.isTop ? '取消置顶' : '置顶' }}
          </a>
          <a @click.stop="delTodo(item)" class="del-btn">删除</a>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: [
    "todos",
    "formatTime",
    "priorityOptions",
    "getPriorityColor"
  ],
  methods: {
    delTodo(item) {
      this.$emit("delTodo", item)
    },
    toggleTop(item) {
      this.$emit("toggleTop", item)
    },
    handlePriorityChange(todoId, priority) {
      this.$emit("changePriority", todoId, priority)
    }
  }
}
</script>

<style scoped>
.tdList {
  list-style: none;
  padding: 0;
  text-align: left;
  background-color: #fff;
  border-radius: 10px;
  width: calc(50ch + 220px);
  margin: 0 auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.tdItem {
  padding: 10px 20px; 
  border-bottom: 1px solid #ddd;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: background-color 0.2s;
  overflow: hidden; 
  position: relative; 
}

.tdItem:last-child {
  border-bottom: 0;
}

.tdItem:hover {
  background-color: #fafafa;
}

.tdToggle {
  cursor: pointer;
  width: 16px;
  height: 16px;
  margin-top: 2px;
  margin-right: 10px;
  flex-shrink: 0; 
}

.tdItem-main {
  display: flex;
  align-items: center;
  flex: 1;
  
  
  overflow: hidden;
}

.tdContent {
  display: flex;
  flex-direction: column;
  justify-content: center;
  flex: 1; 
  overflow: hidden; 
  
}

.tdContainer {
  margin-top: 16px; 
}

.tdTxt-wrap {
  display: flex;
  align-items: center;
}

.priority-tag {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  margin-right: 8px;
}

.tdTxt {
  padding-left: 0;
  font-size: 14px;
  display: flex;
  align-items: center;
}

.completed {
  text-decoration: line-through;
  color: #999;
}

.top-tag {
  background-color: #ff7a45;
  color: #fff;
  font-size: 10px;
  padding: 2px 8px;
  border-radius: 12px;
  margin-left: 8px;
  vertical-align: middle;
}

.completed .top-tag {
  background-color: #ffb899;
}

.tdCreateTime {
  padding-left: 18px;
  font-size: 11px;
  color: #bbb;
  margin-top: 2px;
}

.completed+.tdCreateTime {
  color: #ccc;
}

.tdItem-acts {
  display: none;
  gap: 10px;
  align-items: center;
  position: absolute; 
  right: 8px; 
  top: 50%;
  transform: translateY(-50%); 
  background-color: inherit; 
}

.tdItem:hover .tdItem-acts {
  display: flex;
}

.priority-select {
  padding: 2px 6px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 12px;
  cursor: pointer;
  background-color: #fff;
}

.top-btn,
.del-btn {
  text-decoration: none;
  cursor: pointer;
  font-size: 13px;
  padding: 4px 8px;
  border-radius: 4px;
  transition: all 0.2s;
}

.top-btn {
  color: #1890ff;
  border: 1px solid #e6f7ff;
  background-color: #f0f9ff;
}

.top-btn:hover {
  color: #096dd9;
  background-color: #e6f7ff;
}

.top-btn:has(+ .del-btn) {
  color: #faad14;
  border: 1px solid #fff7e6;
  background-color: #fffbe6;
}

.top-btn:has(+ .del-btn):hover {
  color: #d48806;
  background-color: #fff7e6;
}

.del-btn {
  color: #fff;
  background-color: #ff4d4f;
  border: 1px solid #ff4d4f;
}

.del-btn:hover {
  background-color: #f5222d;
  border-color: #f5222d;
}
</style>
