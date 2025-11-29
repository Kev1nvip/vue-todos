<template>
    <div class="tdFooter">
        <!-- 对 count 做兜底处理，空值时显示 0 -->
        <span>总计：{{ count ?? 0 }}</span>
        <div class="tdTabs">
            <a :class="{active: tabType === 0}" @click="tabClick(0)">全部</a>
            <a :class="{active: tabType === 1}" @click="tabClick(1)">未完成</a>
            <a :class="{active: tabType === 2}" @click="tabClick(2)">已完成</a>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        // 明确指定 count 类型和默认值，增强健壮性
        count: {
            type: Number,
            default: 0 // 父组件未传时默认显示 0
        },
        tabType: {
            type: Number,
            default: 0
        }
    },
    methods: {
        tabClick(newTabtype) {
            this.$emit("changeTabType", newTabtype);
        }
    }
}
</script>

<style scoped>
.tdFooter {
    background-color: #fff;
    max-width: 750px;
    margin: 16px auto;
    padding: 10px 20px;
    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    /* 确保文字不被遮挡，增加字体可见性 */
    font-size: 15px;
    color: #333; /* 避免继承父元素的透明色 */
}

.tdTabs a {
    padding: 0 10px;
    cursor: pointer;
    text-decoration: none; /* 移除默认下划线，可选 */
}

.active {
    color: red;
}
</style>