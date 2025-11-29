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
  box-sizing: border-box;
  /* 关键：限制容器最大宽度为屏幕宽度（减去内边距），避免横向溢出 */
  max-width: calc(100vw - 24px);
  overflow-x: hidden; /* 隐藏横向滚动条 */
}

.tdList {
  list-style: none;
  padding: 0;
  text-align: left;
  background-color: #fff;
  border-radius: 10px;
  width: 100%;
  /* 移除固定max-width，改为随容器自适应 */
  max-width: 100%;
  margin: 0 auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  box-sizing: border-box;
}

.tdItem {
  padding: 12px 16px;
  border-bottom: 1px solid #eee;
  cursor: pointer;
  display: flex;
  align-items: flex-start;
  /* 关键：取消justify-content: space-between，避免强制撑开宽度 */
  gap: 12px;
  transition: background-color 0.2s ease;
  overflow: visible;
  width: 100%;
  box-sizing: border-box;
}

/* 文本区域 - 核心限制宽度 */
.tdItem-main {
  display: flex;
  align-items: flex-start;
  flex: 1;
  min-width: 0;
  /* 确保文本区域不超过容器宽度（减去按钮区域最小宽度） */
  max-width: calc(100% - 160px);
}

.tdContent {
  display: flex;
  flex-direction: column;
  justify-content: center;
  flex: 1;
  min-width: 0;
}

.tdTxt-wrap {
  display: flex;
  align-items: flex-start;
  min-width: 0;
}

.tdTxt {
  padding-left: 0;
  font-size: 13px;
  display: inline-block;
  line-height: 1.6;
  white-space: normal;
  word-wrap: break-word;
  overflow-wrap: break-word;
  overflow: visible;
  min-width: 0;
  /* 文本最大宽度限制为父容器的100%，确保不溢出 */
  max-width: 100%;
}

/* 按钮区域 - 严格控制宽度 */
.tdItem-acts {
  display: none;
  gap: 6px;
  align-items: center;
  flex-shrink: 0;
  /* 固定按钮区域最大宽度，避免挤压文本 */
  max-width: 150px;
  width: auto;
  margin-left: auto;
  padding: 4px 0;
}

/* 移动端强制按钮区域不撑开容器 */
@media (max-width: 767px) {
  .tdContainer {
    /* 进一步收紧容器宽度，留足安全距离 */
    max-width: calc(100vw - 32px);
    padding: 0 8px;
  }

  .tdItem {
    padding: 10px 12px;
  }

  .tdItem-main {
    /* 移动端文本区域占比更高 */
    max-width: calc(100% - 140px);
  }

  .tdItem-acts {
    max-width: 130px;
    gap: 4px;
  }

  /* 长文本时按钮区域允许换行到下方 */
  .tdItem {
    flex-wrap: wrap;
  }
}

/* 其他原有样式保持不变 */
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

.tdToggle {
  cursor: pointer;
  width: 20px;
  height: 20px;
  margin-top: 2px;
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

.priority-tag {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  margin-right: 6px;
  margin-top: 6px;
  flex-shrink: 0;
}

.completed {
  text-decoration: line-through;
  color: #999;
  transition: color 0.2s ease;
}

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
  display: inline-block;
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

@media (min-width: 768px) {
  .tdContainer {
    padding: 0;
    max-width: 750px; /* 桌面端恢复固定宽度 */
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
    max-width: 200px;
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