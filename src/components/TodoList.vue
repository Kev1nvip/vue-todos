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
  box-sizing: border-box; /* 确保内边距不影响整体宽度 */
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
  box-sizing: border-box;
}

.tdItem {
  padding: 12px 16px;
  border-bottom: 1px solid #eee;
  cursor: pointer;
  display: flex;
  align-items: flex-start; /* 顶部对齐，避免文本换行后错位 */
  justify-content: space-between;
  gap: 12px;
  transition: background-color 0.2s ease;
  overflow: visible;
  width: 100%;
  box-sizing: border-box; /* 确保padding不导致宽度溢出 */
}

.tdItem:hover {
  background-color: #f5f8ff;
  border-bottom-color: #e8f0fe;
}

.tdItem:hover .completed {
  color: #888;
}

.tdItem:last-child {
  border-bottom: 0;
}

/* 复选框样式 - 确保垂直居中 */
.tdToggle {
  cursor: pointer;
  width: 20px;
  height: 20px;
  margin-top: 2px; /* 微调位置，与首行文本对齐 */
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

/* 文本区域 - 核心修改 */
.tdItem-main {
  display: flex;
  align-items: flex-start; /* 顶部对齐，支持多行文本 */
  flex: 1;
  min-width: 0; /* 关键：允许文本区域收缩，避免挤压按钮 */
}

.tdContent {
  display: flex;
  flex-direction: column;
  justify-content: center;
  flex: 1;
  min-width: 0; /* 确保文本能被容器约束 */
}

.tdTxt-wrap {
  display: flex;
  align-items: flex-start; /* 顶部对齐 */
  min-width: 0;
}

.priority-tag {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  margin-right: 6px;
  margin-top: 6px; /* 与文本首行对齐 */
  flex-shrink: 0;
}

.tdTxt {
  padding-left: 0;
  font-size: 13px;
  display: inline-block; /* 改为块级显示，确保换行生效 */
  line-height: 1.6;
  /* 确保长文本换行并隐藏溢出 */
  white-space: normal;
  word-wrap: break-word;
  overflow-wrap: break-word;
  overflow: visible; /* 允许文本自然显示，不截断 */
  min-width: 0;
}

/* 确保未完成文本正常显示 */
.completed {
  text-decoration: line-through;
  color: #999;
  transition: color 0.2s ease;
}
/* 新增：未完成文本强制显示黑色 */
.tdTxt:not(.completed) {
  color: #333;
}

.top-tag {
  background-color: #ff7a45;
  color: #fff;
  font-size: 9px;
  padding: 1px 6px;
  border-radius: 8px;
  margin-left: 6px;
  vertical-align: middle;
  display: inline-block; /* 避免标签影响文本换行 */
}

.completed .top-tag {
  background-color: #ffb899;
}

.tdCreateTime {
  padding-left: 14px;
  font-size: 10px;
  color: #bbb;
  margin-top: 4px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.completed+.tdCreateTime {
  color: #ccc;
}

/* 按钮区域 - 适配移动端 */
.tdItem-acts {
  display: none;
  gap: 6px;
  align-items: center;
  flex-shrink: 0;
  min-width: 150px;
  margin-left: auto;
  padding: 4px 0; /* 垂直居中对齐 */
}

.tdItem:hover .tdItem-acts {
  display: flex;
}

/* 修复按钮区域在小屏幕换行问题 */
@media (max-width: 767px) {
  .tdItem-acts {
    flex-wrap: wrap; /* 允许按钮换行 */
    justify-content: flex-end; /* 靠右对齐 */
    gap: 4px;
    min-width: auto; /* 取消最小宽度限制 */
    width: auto;
  }
  
  .priority-select {
    padding: 1px 3px;
    font-size: 10px;
  }
  
  .top-btn, .del-btn {
    font-size: 11px;
    padding: 2px 4px;
    min-width: 30px;
  }
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
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
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

  .tdItem:hover {
    background-color: #eaf2ff;
  }
}
</style>