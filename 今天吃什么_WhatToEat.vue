<template>
  <div class="app">
    <h1 class="title">🍜 今天吃什么？</h1>
    <p class="subtitle">—— 随机美食决策器，专治选择困难症 ——</p>

    <!-- 1. v-model：输入框和 name 双向绑定，边打字边生效 -->
    <div class="input-box">
      <input
        v-model="name"
        class="input"
        type="text"
        maxlength="12"
        placeholder="请输入你的名字…"
      />
    </div>

    <!-- 2. v-if / v-else：根据“有没有输入名字”显示不同内容 -->
    <div class="welcome-box">
      <p v-if="name" class="welcome">
        <!-- 3. v-text：把文本渲染进标签里（和 {{ }} 效果一样） -->
        <span v-text="welcomeText"></span>
      </p>
      <p v-else class="welcome welcome-empty">👋 先告诉我你的名字，我再帮你决定～</p>
    </div>

    <!-- 4. v-bind（简写 :）：动态绑定 class、style、src、title 等属性 -->
    <div
      class="result-card"
      :class="priceLevel.key"
      :style="{ borderColor: priceLevel.color }"
    >
      <div class="emoji" :title="currentFood.name">{{ currentFood.emoji }}</div>
      <h2 class="food-name">{{ currentFood.name }}</h2>
      <p class="price">￥{{ currentFood.price }}</p>
      <span class="badge" :style="{ background: priceLevel.color }">
        {{ priceLevel.label }}
      </span>
      <!-- 5. v-html：把带 HTML 标签的字符串渲染成真正的 HTML -->
      <p class="tip" v-html="tipHtml"></p>
    </div>

    <!-- 6. v-on（简写 @click）：绑定点击事件 -->
    <div class="btn-row">
      <button class="btn btn-dice" @click="rollFood">🎲 换一个</button>
      <button class="btn btn-ghost" @click="showMenu = !showMenu">
        {{ showMenu ? '🙈 收起菜单' : '📖 查看完整菜单' }}
      </button>
    </div>
    <p class="count">你已经纠结了 <b>{{ rollCount }}</b> 次</p>

    <!-- 7. v-show：通过 display 控制显示/隐藏（元素一直存在） -->
    <div class="menu" v-show="showMenu">
      <h3 class="menu-title">📋 全部候选（{{ foods.length }} 种）</h3>
      <!-- 8. v-for：循环渲染列表，:key 必须写 -->
      <ul class="menu-list">
        <li
          v-for="food in foods"
          :key="food.id"
          class="menu-item"
          :class="{ active: food.id === currentFood.id }"
          @click="pickFood(food)"
        >
          <span class="item-emoji">{{ food.emoji }}</span>
          <span class="item-name">{{ food.name }}</span>
          <span class="item-price">￥{{ food.price }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// ---- 数据 ----
const name = ref('')
const showMenu = ref(true)
const rollCount = ref(0)
const currentIndex = ref(0)

// 至少 6 种美食：id、名称、emoji、价格
const foods = ref([
  { id: 1, name: '兰州拉面', emoji: '🍜', price: 15 },
  { id: 2, name: '黄焖鸡米饭', emoji: '🍗', price: 20 },
  { id: 3, name: '麻辣烫', emoji: '🌶️', price: 22 },
  { id: 4, name: '煎饼果子', emoji: '🥞', price: 8 },
  { id: 5, name: '寿司拼盘', emoji: '🍣', price: 35 },
  { id: 6, name: '汉堡薯条', emoji: '🍔', price: 28 },
  { id: 7, name: '螺蛳粉', emoji: '🍲', price: 18 },
  { id: 8, name: '牛肉火锅', emoji: '🥘', price: 58 }
])

// ---- 计算属性 ----
const currentFood = computed(() => foods.value[currentIndex.value])

const welcomeText = computed(() => {
  return `🎉 你好呀，${name.value}！今天吃「${currentFood.value.name}」怎么样？`
})

// 根据价格判断档位
const priceLevel = computed(() => {
  const price = currentFood.value.price
  if (price <= 15) {
    return { key: 'cheap', label: '省钱模式', color: '#22c55e' }
  } else if (price <= 30) {
    return { key: 'normal', label: '正常消费', color: '#f59e0b' }
  } else {
    return { key: 'expensive', label: '奢侈一把', color: '#ef4444' }
  }
})

// 根据价格给出不同提示（v-html 使用）
const tipHtml = computed(() => {
  const price = currentFood.value.price
  if (price <= 15) {
    return `只要 <b>￥${price}</b>，便宜又管饱，学生党冲！💪`
  } else if (price <= 30) {
    return `大概 <b>￥${price}</b>，价格刚刚好，可以接受～😋`
  } else {
    return `要花 <b>￥${price}</b>，今天对自己好一点！🎉`
  }
})

// ---- 方法 ----
// 随机换一个美食
function rollFood() {
  let next = Math.floor(Math.random() * foods.value.length)
  // 如果随机到同一个，就往后移一位，保证每次都能换
  if (next === currentIndex.value) {
    next = (next + 1) % foods.value.length
  }
  currentIndex.value = next
  rollCount.value = rollCount.value + 1
}

// 点击菜单中的某一项，直接选它
function pickFood(food) {
  currentIndex.value = foods.value.indexOf(food)
}
</script>

<style scoped>
.app {
  max-width: 560px;
  margin: 0 auto;
  padding: 24px 18px 48px;
  text-align: center;
  font-family: system-ui, 'Segoe UI', 'Microsoft YaHei', sans-serif;
  color: #3a3a4a;
}

.title {
  margin: 0 0 6px;
  font-size: 40px;
  color: #ff6b6b;
  letter-spacing: 2px;
}

.subtitle {
  margin: 0 0 24px;
  color: #9a9ab0;
  font-size: 15px;
}

/* 输入框 */
.input-box {
  margin-bottom: 14px;
}

.input {
  width: 70%;
  padding: 12px 16px;
  font-size: 16px;
  color: #3a3a4a;
  border: 2px solid #ffd6d6;
  border-radius: 999px;
  outline: none;
  transition: border-color 0.2s;
}

.input:focus {
  border-color: #ff6b6b;
}

/* 欢迎语 */
.welcome-box {
  min-height: 28px;
  margin-bottom: 20px;
}

.welcome {
  margin: 0;
  font-size: 17px;
  font-weight: 600;
  color: #ff6b6b;
}

.welcome-empty {
  font-weight: 400;
  color: #b0b0c0;
}

/* 推荐卡片 */
.result-card {
  padding: 28px 20px 24px;
  background: #fff;
  border: 3px solid #ffd6d6;
  border-radius: 24px;
  box-shadow: 0 10px 24px rgba(255, 107, 107, 0.15);
  transition: border-color 0.3s;
}

.emoji {
  font-size: 76px;
  line-height: 1;
  animation: bounce 1.6s ease-in-out infinite;
}

.food-name {
  margin: 12px 0 6px;
  font-size: 28px;
  color: #33334a;
}

.price {
  margin: 0 0 12px;
  font-size: 20px;
  font-weight: 700;
  color: #ff8c42;
}

.badge {
  display: inline-block;
  padding: 4px 14px;
  font-size: 13px;
  color: #fff;
  border-radius: 999px;
}

.tip {
  margin: 14px 0 0;
  font-size: 15px;
  color: #6b6b80;
}

/* 按钮 */
.btn-row {
  display: flex;
  gap: 12px;
  justify-content: center;
  margin: 24px 0 8px;
  flex-wrap: wrap;
}

.btn {
  padding: 12px 24px;
  font-size: 16px;
  font-weight: 600;
  color: #fff;
  cursor: pointer;
  border: none;
  border-radius: 999px;
  transition: transform 0.15s, box-shadow 0.15s;
}

.btn:hover {
  transform: translateY(-2px);
}

.btn-dice {
  background: linear-gradient(135deg, #ff6b6b, #ffa07a);
  box-shadow: 0 6px 16px rgba(255, 107, 107, 0.4);
}

.btn-ghost {
  color: #ff6b6b;
  background: #fff;
  border: 2px solid #ffd6d6;
}

.count {
  margin: 0 0 20px;
  font-size: 14px;
  color: #a0a0b5;
}

.count b {
  color: #ff6b6b;
}

/* 菜单 */
.menu {
  padding: 18px;
  background: #fffaf5;
  border: 2px dashed #ffd6d6;
  border-radius: 20px;
  text-align: left;
}

.menu-title {
  margin: 0 0 12px;
  font-size: 17px;
  color: #ff6b6b;
  text-align: center;
}

.menu-list {
  list-style: none;
  margin: 0;
  padding: 0;
}

.menu-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 14px;
  margin-bottom: 8px;
  cursor: pointer;
  background: #fff;
  border: 2px solid transparent;
  border-radius: 14px;
  transition: border-color 0.2s, transform 0.15s;
}

.menu-item:hover {
  transform: translateX(4px);
}

.menu-item.active {
  border-color: #ff6b6b;
  background: #fff1f1;
}

.item-emoji {
  font-size: 22px;
}

.item-name {
  flex: 1;
  font-size: 15px;
  color: #44445a;
}

.item-price {
  font-size: 14px;
  font-weight: 600;
  color: #ff8c42;
}

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}
</style>
