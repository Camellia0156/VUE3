<template>
  <div class="page">

    <!-- ======================== -->
    <!-- 页面标题 -->
    <!-- ======================== -->
    <header class="header">
      <div class="logo">🎬</div>
      <h1>我的电影票选座助手</h1>
      <p>选电影 · 选座位 · 快乐观影</p>
    </header>


    <!-- ======================== -->
    <!-- 任务1：内容渲染 -->
    <!-- ======================== -->
    <section class="card">
      <h2>🎞 今日推荐</h2>

      <h3 v-text="recommendTitle"></h3>

      <p
        class="activity"
        v-html="activityMessage"
      >
      </p>
    </section>


    <!-- ======================== -->
    <!-- 任务2：电影列表 -->
    <!-- ======================== -->
    <section class="card">
      <h2>🔥 正在热映</h2>

      <div class="movie-list">

        <div
          v-for="movie in movies"
          :key="movie.id"
          class="movie-card"
          @click="selectMovie(movie)"
        >
          <div class="poster">
            {{ movie.icon }}
          </div>

          <div class="movie-info">
            <h3>{{ movie.name }}</h3>
            <p>⭐ {{ movie.score }} 分</p>
            <p>{{ movie.type }}</p>
            <strong>￥{{ movie.price }}</strong>
          </div>
        </div>

      </div>
    </section>


    <!-- ======================== -->
    <!-- 当前选择 -->
    <!-- ======================== -->
    <section class="card selected-movie">
      <h2>🎬 当前选择</h2>

      <div class="current-movie">

        <div class="big-icon">
          {{ selectedMovie.icon }}
        </div>

        <div>
          <h2>{{ selectedMovie.name }}</h2>

          <p>
            {{ selectedMovie.type }}
          </p>

          <p class="price">
            ￥{{ selectedMovie.price }} / 张
          </p>
        </div>

      </div>
    </section>


    <!-- ======================== -->
    <!-- 任务3：属性绑定 + 事件绑定 -->
    <!-- ======================== -->
    <section class="card">
      <h2>🎫 选择票数</h2>

      <div class="counter">

        <button
          :disabled="ticketCount <= 1"
          @click="decreaseTicket"
        >
          −
        </button>

        <div class="ticket-number">
          {{ ticketCount }}
        </div>

        <button
          :disabled="ticketCount >= 6"
          @click="increaseTicket"
        >
          +
        </button>

      </div>

      <p class="tip">
        每次最多购买 6 张电影票
      </p>
    </section>


    <!-- ======================== -->
    <!-- 任务4：双向数据绑定 -->
    <!-- ======================== -->
    <section class="card">
      <h2>👤 填写购票人</h2>

      <input
        v-model="username"
        class="name-input"
        type="text"
        placeholder="请输入你的名字"
      >

      <p class="welcome">
        👋 你好，{{ username || '同学' }}！
      </p>
    </section>


    <!-- ======================== -->
    <!-- 任务5：条件渲染 -->
    <!-- ======================== -->
    <section class="card">
      <h2>💎 会员身份</h2>

      <button
        class="vip-button"
        @click="changeVip"
      >
        切换 VIP 身份
      </button>

      <div
        v-if="isVip"
        class="vip-message"
      >
        🎁 VIP 用户享受 8 折优惠
      </div>

      <div
        v-else
        class="normal-message"
      >
        👤 当前为普通用户
      </div>

      <p class="tip">
        当前 isVip：{{ isVip }}
      </p>
    </section>


    <!-- ======================== -->
    <!-- 任务6：列表渲染——座位 -->
    <!-- ======================== -->
    <section class="card">
      <h2>💺 选择座位</h2>

      <div class="screen">
        银幕 SCREEN
      </div>

      <div class="seat-area">

        <!--
          座位批量渲染要点：
          ① v-for="seat in seats" 批量生成
          ② :key="seat.id" 提供稳定 key
          ③ :disabled="seat.sold" 禁用已售座位
          ④ :class 绑定根据状态切换样式
          ⑤ @click="toggleSeat(seat)" 处理点击
        -->

        <button
          v-for="seat in seats"
          :key="seat.id"
          class="seat"
          :class="{
            selectedSeat: selectedSeats.includes(seat.id),
            sold: seat.sold
          }"
          :disabled="seat.sold"
          @click="toggleSeat(seat)"
        >
          {{ seat.name }}
        </button>

      </div>


      <div class="seat-tip">
        <span>⬜ 可选</span>
        <span>✅ 已选</span>
        <span>❌ 已售</span>
      </div>

      <p class="tip">
        已选择：
        {{ selectedSeats.length }}
        个座位
      </p>
    </section>


    <!-- ======================== -->
    <!-- 任务7：v-show -->
    <!-- ======================== -->
    <section class="card">

      <button
        class="rule-button"
        @click="showRule = !showRule"
      >
        查看 / 收起购票须知
      </button>

      <div
        v-show="showRule"
        class="rules"
      >
        <p>① 每人最多购买 6 张电影票。</p>
        <p>② 购票数量要和选座数量一致。</p>
        <p>③ 已售出的座位不能选择。</p>
        <p>④ VIP 用户享受 8 折优惠。</p>
      </div>

    </section>


    <!-- ======================== -->
    <!-- 订单 -->
    <!-- ======================== -->
    <section class="card order">

      <h2>🧾 我的订单</h2>

      <div class="order-row">
        <span>购票人</span>
        <strong>
          {{ username || '未填写' }}
        </strong>
      </div>

      <div class="order-row">
        <span>电影</span>
        <strong>
          {{ selectedMovie.name }}
        </strong>
      </div>

      <div class="order-row">
        <span>电影票</span>
        <strong>
          {{ ticketCount }} 张
        </strong>
      </div>

      <div class="order-row">
        <span>座位</span>
        <strong>
          {{ seatText }}
        </strong>
      </div>

      <div class="order-row">
        <span>会员身份</span>
        <strong>
          {{ isVip ? 'VIP会员' : '普通用户' }}
        </strong>
      </div>

      <div class="order-row">
        <span>原价</span>
        <span>
          ￥{{ originalPrice }}
        </span>
      </div>

      <div
        v-if="isVip"
        class="order-row discount"
      >
        <span>VIP优惠</span>

        <span>
          - ￥{{ discountPrice }}
        </span>
      </div>


      <div class="total">
        <span>应付金额</span>

        <strong>
          ￥{{ totalPrice }}
        </strong>
      </div>


      <button
        :disabled="!canSubmit"
        class="submit-button"
        @click="submitOrder"
      >
        🎫 确认购票
      </button>


      <p class="submit-tip">
        请填写姓名，并选择与票数相同数量的座位
      </p>

    </section>


    <!-- ======================== -->
    <!-- 购票成功 -->
    <!-- ======================== -->

    <!-- 购票成功弹窗：v-if="orderSuccess" 控制显隐 -->
    <div
      v-if="orderSuccess"
      class="success-mask"
    >
      <div class="success-box">

        <div class="success-icon">
          🎉
        </div>

        <h2>购票成功！</h2>

        <p>
          {{ username }}，祝你观影愉快！
        </p>

        <p>
          🎬 {{ selectedMovie.name }}
        </p>

        <p>
          💺 {{ seatText }}
        </p>

        <button @click="orderSuccess = false">
          确定
        </button>

      </div>
    </div>

  </div>
</template>


<script setup>
import { computed, ref } from 'vue'


// ========================
// 内容渲染
// ========================

const recommendTitle = ref('本周热门电影')

const activityMessage = ref(
  '🎁 <strong>周末活动：</strong> VIP 用户购票享受 8 折优惠！'
)


// ========================
// 电影列表
// ========================

const movies = ref([
  {
    id: 1,
    name: '疯狂动物城2',
    icon: '🦊',
    type: '动画 / 喜剧',
    score: 9.2,
    price: 35
  },
  {
    id: 2,
    name: '哪吒2',
    icon: '🔥',
    type: '动画 / 奇幻',
    score: 9.5,
    price: 39
  },
  {
    id: 3,
    name: '流浪地球3',
    icon: '🌍',
    type: '科幻 / 冒险',
    score: 9.1,
    price: 42
  }
])


// 当前选择的电影
const selectedMovie = ref(movies.value[0])


// 点击电影
function selectMovie(movie) {
  selectedMovie.value = movie
}


// ========================
// 购票数量
// ========================

const ticketCount = ref(1)


// 增加票数
function increaseTicket() {
  if (ticketCount.value < 6) {
    ticketCount.value++
  }
}


// 减少票数
function decreaseTicket() {
  if (ticketCount.value > 1) {
    ticketCount.value--
  }
}


// ========================
// 用户姓名
// ========================

const username = ref('')





// ========================
// VIP
// ========================

const isVip = ref(false)

function changeVip() {
  isVip.value = !isVip.value
}


// ========================
// 座位
// ========================

const seats = ref([
  { id: 1, name: 'A1', sold: false },
  { id: 2, name: 'A2', sold: false },
  { id: 3, name: 'A3', sold: true },
  { id: 4, name: 'A4', sold: false },
  { id: 5, name: 'A5', sold: false },

  { id: 6, name: 'B1', sold: false },
  { id: 7, name: 'B2', sold: true },
  { id: 8, name: 'B3', sold: false },
  { id: 9, name: 'B4', sold: false },
  { id: 10, name: 'B5', sold: false },

  { id: 11, name: 'C1', sold: false },
  { id: 12, name: 'C2', sold: false },
  { id: 13, name: 'C3', sold: false },
  { id: 14, name: 'C4', sold: true },
  { id: 15, name: 'C5', sold: false }
])


// 已选座位
const selectedSeats = ref([])


// 点击座位
function toggleSeat(seat) {

  if (seat.sold) {
    return
  }

  const index =
    selectedSeats.value.indexOf(seat.id)

  // 已经选中，再次点击取消
  if (index !== -1) {
    selectedSeats.value.splice(index, 1)
    return
  }

  // 不能超过购票数量
  if (
    selectedSeats.value.length >=
    ticketCount.value
  ) {
    alert(
      `最多只能选择 ${ticketCount.value} 个座位`
    )

    return
  }

  selectedSeats.value.push(seat.id)
}


// ========================
// 购票须知
// ========================

const showRule = ref(false)


// ========================
// 原价
// ========================

const originalPrice = computed(() => {

  return (
    selectedMovie.value.price *
    ticketCount.value
  )

})


// ========================
// VIP 优惠
// ========================

const discountPrice = computed(() => {

  if (!isVip.value) {
    return 0
  }

  return Math.round(
    originalPrice.value * 0.2
  )

})


// ========================
// 最终价格
// ========================

const totalPrice = computed(() => {

  if (isVip.value) {
    return Math.round(
      originalPrice.value * 0.8
    )
  }

  return originalPrice.value
})


// ========================
// 显示座位名称
// ========================

const seatText = computed(() => {

  if (selectedSeats.value.length === 0) {
    return '未选择'
  }

  return selectedSeats.value
    .map(id => {

      const seat = seats.value.find(
        item => item.id === id
      )

      return seat ? seat.name : ''

    })
    .join('、')

})


// ========================
// 是否允许提交
// ========================

const canSubmit = computed(() => {

  return (
    username.value.trim() !== '' &&
    selectedSeats.value.length ===
      ticketCount.value
  )

})


// ========================
// 订单提交
// ========================

const orderSuccess = ref(false)

function submitOrder() {

  if (!canSubmit.value) {
    alert('请先完成购票信息')
    return
  }

  orderSuccess.value = true
}

</script>


<style scoped>
.page {
  width: 820px;
  max-width: 92%;
  margin: 30px auto 80px;

  font-family:
    Arial,
    "Microsoft YaHei",
    sans-serif;

  color: #333;
}


.header {
  padding: 35px 20px;
  margin-bottom: 25px;

  text-align: center;

  color: white;

  background:
    linear-gradient(
      135deg,
      #667eea,
      #764ba2
    );

  border-radius: 20px;
}

.logo {
  font-size: 60px;
}

.header h1 {
  margin: 10px 0;
}

.header p {
  margin: 0;
}


.card {
  margin-bottom: 22px;
  padding: 24px;

  background: white;

  border: 1px solid #e5e5e5;
  border-radius: 16px;

  box-shadow:
    0 6px 18px rgba(0, 0, 0, 0.06);
}

.card h2 {
  margin-top: 0;
}


.activity {
  padding: 12px 16px;

  background: #fff6df;

  border-radius: 10px;
}


.movie-list {
  display: grid;

  grid-template-columns:
    repeat(3, 1fr);

  gap: 15px;
}


.movie-card {
  padding: 18px;

  cursor: pointer;

  border: 2px solid #eee;
  border-radius: 14px;

  transition: 0.2s;
}


.movie-card:hover {
  transform: translateY(-3px);

  border-color: #8c7ae6;
}


.poster {
  font-size: 55px;
  text-align: center;
}


.movie-info p {
  color: #666;
}


.movie-info strong {
  color: #e74c3c;

  font-size: 20px;
}


.selected-movie {
  background: #f7f5ff;
}


.current-movie {
  display: flex;
  align-items: center;

  gap: 25px;
}


.big-icon {
  font-size: 75px;
}


.price {
  color: #e74c3c;

  font-size: 20px;
  font-weight: bold;
}


.counter {
  display: flex;
  align-items: center;

  gap: 20px;
}


.counter button {
  width: 50px;
  height: 45px;

  font-size: 24px;

  border: none;
  border-radius: 10px;

  cursor: pointer;
}


.counter button:disabled {
  cursor: not-allowed;

  opacity: 0.35;
}


.ticket-number {
  width: 60px;

  text-align: center;

  font-size: 28px;
  font-weight: bold;
}


.tip {
  color: #777;
}


.name-input {
  width: 100%;

  box-sizing: border-box;

  padding: 13px 15px;

  font-size: 17px;

  border: 2px solid #ddd;
  border-radius: 10px;
}


.welcome {
  color: #27ae60;

  font-weight: bold;
}


.vip-button,
.rule-button {
  padding: 10px 18px;

  border: none;
  border-radius: 9px;

  cursor: pointer;
}


.vip-message {
  margin-top: 15px;
  padding: 12px;

  background: #fff2c7;

  border-radius: 8px;
}


.normal-message {
  margin-top: 15px;
  padding: 12px;

  background: #f2f2f2;

  border-radius: 8px;
}


.screen {
  width: 70%;

  margin: 20px auto 35px;
  padding: 10px;

  text-align: center;

  background: #eee;

  border-radius:
    50% 50% 10px 10px;
}


.seat-area {
  display: grid;

  grid-template-columns:
    repeat(5, 1fr);

  gap: 12px;

  max-width: 500px;

  margin: auto;
}


.seat {
  padding: 12px 5px;

  cursor: pointer;

  border: none;
  border-radius: 8px;

  background: #dfe6e9;
}


.seat.selectedSeat {
  color: white;

  background: #00b894;
}


.seat.sold {
  color: white;

  background: #636e72;
}


.seat-tip {
  display: flex;
  justify-content: center;

  gap: 25px;

  margin-top: 25px;
}


.rules {
  margin-top: 15px;
  padding: 15px;

  background: #f7f7f7;

  border-radius: 10px;
}


.order {
  background: #fafafa;
}


.order-row {
  display: flex;
  justify-content: space-between;

  padding: 11px 0;

  border-bottom:
    1px dashed #ddd;
}


.discount {
  color: #27ae60;
}


.total {
  display: flex;
  justify-content: space-between;

  margin-top: 20px;

  font-size: 22px;
}


.total strong {
  color: #e74c3c;

  font-size: 28px;
}


.submit-button {
  width: 100%;

  margin-top: 20px;
  padding: 14px;

  font-size: 18px;
  font-weight: bold;

  color: white;

  background:
    linear-gradient(
      135deg,
      #667eea,
      #764ba2
    );

  border: none;
  border-radius: 12px;

  cursor: pointer;
}


.submit-button:disabled {
  opacity: 0.45;
}


.submit-tip {
  text-align: center;

  color: #999;

  font-size: 14px;
}


.success-mask {
  position: fixed;

  inset: 0;

  display: flex;
  align-items: center;
  justify-content: center;

  background:
    rgba(0, 0, 0, 0.5);
}


.success-box {
  width: 350px;

  padding: 35px;

  text-align: center;

  background: white;

  border-radius: 20px;
}


.success-icon {
  font-size: 65px;
}


.success-box button {
  padding: 10px 30px;

  color: white;

  background: #6c5ce7;

  border: none;
  border-radius: 8px;

  cursor: pointer;
}


@media (max-width: 700px) {

  .movie-list {
    grid-template-columns: 1fr;
  }

}
</style>