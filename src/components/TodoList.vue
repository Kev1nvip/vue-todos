<template>
  <div class="tdContainer">
    <ul class="tdList">
      <li class="tdItem" v-for="item in todos" :key="item.id">
        <div class="tdItem-main">
          <input 
            type="checkbox" 
            v-model="item.completed" 
            class="tdToggle" 
            aria-label="标记完成"
          />
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
.tdContainer {
  margin-top: 16px;
  padding: 0 12px;
}

.tdList {
  list-style: none;
  padding: 0;
  text-align: left;
  background-color: #fff;
  border-radius: 10px;
  width: 100%;
  max-width: 750px;
  margin: 0 auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.tdItem {
  padding: 12px 16px;
  border-bottom: 1px solid #eee;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  transition: background-color 0.2s ease; /* 平滑过渡背景色 */
  overflow: hidden;
  width: 100%;
}

/* 核心修改：鼠标悬浮背景变色，突出显示 */
.tdItem:hover {
  background-color: #f5f8ff; /* 淡蓝色背景，醒目且不刺眼 */
  border-bottom-color: #e8f0fe; /*  hover时边框同步变色，更协调 */
}

/* 已完成任务hover时背景色稍浅，区分状态 */
.tdItem:hover .completed {
  color: #888;
}

.tdItem:last-child {
  border-bottom: 0;
}

/* 复选框样式 */
.tdToggle {
  cursor: pointer;
  width: 20px;
  height: 20px;
  margin-top: 0;
  margin-right: 8px;
  flex-shrink: 0;
  border: 1px solid #ddd;
  border-radius: 4px;
  appearance: none;
  -webkit-appearance: none;
  position: relative;
}

.tdToggle:checked {
  background-color: #4e6ef2;
  border-color: #4e6ef2;
}

.tdToggle:checked::after {
  content: "✓";
  position: absolute;
  color: white;
  font-size: 14px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* 文本区域 */
.tdItem-main {
  display: flex;
  align-items: center;
  flex: 1;
  max-width: 60ch;
  overflow: hidden;
}

.tdContent {
  display: flex;
  flex-direction: column;
  justify-content: center;
  flex: 1;
  overflow: hidden;
}

.tdTxt-wrap {
  display: flex;
  align-items: center;
}

.priority-tag {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  margin-right: 6px;
}

.tdTxt {
  padding-left: 0;
  font-size: 13px;
  display: flex;
  align-items: center;
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
  line-height: 1.4;
}

.completed {
  text-decoration: line-through;
  color: #999;
  transition: color 0.2s ease; /* 颜色平滑过渡 */
}

.top-tag {
  background-color: #ff7a45;
  color: #fff;
  font-size: 9px;
  padding: 1px 6px;
  border-radius: 8px;
  margin-left: 6px;
  vertical-align: middle;
}

.completed .top-tag {
  background-color: #ffb899;
}

.tdCreateTime {
  padding-left: 14px;
  font-size: 10px;
  color: #bbb;
  margin-top: 2px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.completed+.tdCreateTime {
  color: #ccc;
}

/* 按钮区域 */
.tdItem-acts {
  display: flex;
  gap: 8px;
  align-items: center;
  flex-shrink: 0;
  min-width: 180px;
}

.tdItem-acts {
  display: none;
  gap: 8px;
  align-items: center;
  flex-shrink: 0;
  min-width: 180px;
}
.tdItem:hover .tdItem-acts {
  display: flex;
}

.priority-select {
  padding: 1px 4px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 11px;
  cursor: pointer;
  background-color: #fff;
  flex-shrink: 0;
}

.top-btn,
.del-btn {
  text-decoration: none;
  cursor: pointer;
  font-size: 12px;
  padding: 3px 6px;
  border-radius: 4px;
  transition: all 0.2s;
  min-width: 36px;
  text-align: center;
  flex-shrink: 0;
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

/* 桌面端适配 */
@media (min-width: 768px) {
  .tdContainer {
    padding: 0;
  }

  .tdList {
    max-width: 750px;
  }

  .tdItem {
    padding: 12px 20px;
    gap: 20px;
  }

  .tdToggle {
    width: 18px;
    height: 18px;
    margin-right: 10px;
  }

  .tdItem-main {
    max-width: 60ch;
  }

  .tdTxt {
    font-size: 14px;
  }

  .tdItem-acts {
    gap: 10px;
    min-width: 200px;
  }

  .priority-select {
    padding: 2px 6px;
    font-size: 12px;
  }

  .top-btn,
  .del-btn {
    font-size: 13px;
    padding: 4px 8px;
  }

  /* 桌面端hover背景色稍深，突出效果更明显 */
  .tdItem:hover {
    background-color: #eaf2ff;
  }
}
</style>