<template>
  <div class="common-layout">
    <el-container>
      <el-header>
        <el-page-header>
          <!-- 图标插槽 -->
          <template #icon>
            <div class="block">
              <el-avatar  :src="circleUrl" />
            </div>
          </template>

          <!-- 标题插槽 -->
          <template #title>
            <div style="font-size: 20px; font-weight: bold; color: black;">{{ Name }}</div>
          </template>

          <template #content>
            <el-row  class="nav" v-if=isLogin>
              <router-link to="/home">
                <el-col  @click="clickNav($event, '首页')" :class="{active:selectedNav === '首页'}">
                  <el-icon><Point /></el-icon>
                  <span class="navText">首页</span>
                </el-col>
              </router-link>
              <router-link to="/shop">
                <el-col @click="clickNav($event, '交易')" :class="{active:selectedNav === '交易'}">
                  <el-icon><Point /></el-icon>
                  <span class="navText">交易</span>
                </el-col>
              </router-link>
              <router-link to="/have">
                <el-col @click="clickNav($event, '持仓')" :class="{active:selectedNav === '持仓'}">
                  <el-icon><Point /></el-icon>
                  <span class="navText">持仓</span>
                </el-col>
              </router-link>
              <router-link to="/history">
                <el-col @click="clickNav($event, '历史订单')" :class="{active:selectedNav === '历史订单'}">
                  <el-icon><Point /></el-icon>
                  <span class="navText">历史订单</span>
                </el-col>
              </router-link>
            </el-row>
            <div class="nav-line" :style="{left:X+'px',width:Wid+'px'}"></div>
          </template>

          <!-- 额外内容插槽 -->
          <template #extra>
            <el-button v-if="isLogin" type="success" color="#9A7BB5" @click="clickLogout">
              <div class="loginButton">
                退出当前账号
              </div>
            </el-button>
            <el-button v-else type="success" color="#9A7BB5" @click="clickRegister">
              <div class="loginButton">
                登录或注册
              </div>
            </el-button>
          </template>
        </el-page-header>
      </el-header>
      <el-container>
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script setup>
import { logout } from '@/router/auth';
import { onMounted, reactive, toRefs , nextTick } from 'vue'
import { useRouter } from 'vue-router';

const router = useRouter();

const state = reactive({
  isLogin: false,
  Name: '游客',
  selectedNav: '首页',
  X: 0,
  Wid: 0,
})

const { circleUrl, isLogin, Name, selectedNav, X, Wid } = toRefs(state)

function clickRegister(){
  router.push('/toLogin');
}

function clickLogout(){
  logout();
  // 清空sessionStorage
  sessionStorage.clear();
  // 刷新页面内容
  location.reload();
}

function clickNav(event, ntext) {
  selectedNav.value = ntext;
  const rect = event.currentTarget.getBoundingClientRect();
  const icon = event.currentTarget.querySelector('i');
  const iconRect = icon.getBoundingClientRect();
  X.value = iconRect.left - iconRect.width - 30 + rect.width/2;
  Wid.value = rect.width/2 + iconRect.width ;
}

onMounted(async () => {
  const username = sessionStorage.getItem('username');
  if (username) {
    Name.value = username;
    isLogin.value = true;

    // 使用 nextTick 等待 .nav .active 元素被渲染出来
    await nextTick();

    // 获取 .nav .active 元素并模拟点击
    const homeButton = document.querySelector('.nav .active');
    const event = new MouseEvent('click', { bubbles: true });
    homeButton.dispatchEvent(event);
  }
});
</script>


<style scoped>
.common-layout .el-header {
  height: 70px;
  padding: 10px 20px;
  background: #f5f5f5;
}

.common-layout .loginButton{
  font-size: 16px;
  color: var(--text-lightest);
  padding:10px 5px;
}

.common-layout .nav {
  width: auto;
  height: 100%;
}

.common-layout .nav .el-col{
  padding: 20px 30px;
  border-radius: 30px;
  justify-content: center;
  letter-spacing: 2px;
  transition: all 0.3s;
}

.common-layout .nav .el-col:hover{
  color: black;
}

.common-layout .nav .el-col .el-icon{
  position:relative;
  top:3px;
  margin-right: 3px;
}

.common-layout .nav-line {
  height: 4px;
  border-radius: 2px;
  background-color: black;
  transform : translateX(-50%);
  position: absolute;
  bottom: 0;
  transition: 0.3s all;
}

.common-layout .active {
  color: black;
  font-weight: bold;
}

@media (max-width: 950px) {
  .navText {
    display: none;
  }
  .common-layout .nav-line {
    width: 18px;
  }
}

</style>