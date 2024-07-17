<template>
  <div class="ring">
    <i style="--clr:#FFC0CB;"></i>
    <i style="--clr:#FFA500;"></i>
    <i style="--clr:#00FFFF;"></i>
    <div class="login">
      <h2>{{ type ? '登录' : '注册' }}</h2>
      <div class="inputBx">
        <input type="text" placeholder="Username" v-model="username"  @focus="handleFocus">
      </div>
      <div class="inputBx">
        <input type="password" placeholder="Password" v-model="password"  @focus="handleFocus">
      </div>
      <div class="inputBx" v-if="!type">
        <input type="password" placeholder="Check Password" v-model="checkPassword"  @focus="handleFocus">
      </div>
      <div class="inputBx">
        <input type="submit" :value="type ? 'Sign in':'Sign up'" @click="submit">
      </div>
      <div class="links">
        <a href="#" @click.prevent="changeSign()">{{!type ? 'Sign in':'Sign up'}}</a>
        <em v-if="unmatch">{{ tip }}</em>
      </div>
    </div>
  </div>
</template>

<script>
import { login } from '../router/auth';
export default {
  data(){
    return {
      type: true,
      unmatch: false,
      username: '',
      password: '',
      checkPassword: '',
      tip:'',
    }
  },
  methods:{
    changeSign(){
      this.type = !this.type;
      this.username = '';
      this.password = '';
      this.checkPassword = '';
      this.unmatch = false;
    },
    submit() {
      if(this.username == ''){
        this.tip = '用户名不能为空';
        this.unmatch = true;
        return;
      }
      if (!this.type) {
        // register
        if(this.password != this.checkPassword){
          this.tip = '确认密码不匹配！';
          this.unmatch = true;
          this.checkPassword = '';
          return;
        }else{
          // 注册逻辑
          const url = 'http://127.0.0.1:12345/regist?username='+this.username+'&pwd='+this.password;
          fetch(url)
              .then(response => response.text())
              .then(data => {
                if(data === 'true'){
                  this.changeSign();
                  this.tip = '注册成功，请登录';
                  this.unmatch = true;
                  this.username = '';
                  this.password = '';
                  this.checkPassword = '';
                }else{
                  this.tip = '用户名冲突，注册失败！';
                  this.unmatch = true;
                  this.password = '';
                  this.checkPassword = '';
                }
                console.log(data);
                console.log('yes');
              })
        }
      }else{
        // login
        const url = 'http://127.0.0.1:12345/login?username='+this.username+'&pwd='+this.password;
        //登录逻辑
        fetch(url)
            .then(response => response.text())
            .then(data => {
              if(data === 'true'){
                sessionStorage.setItem('username',this.username);
                this.$router.push('/index');
                login();
              }else{
                this.tip = '用户不存在或密码错误！';
                this.unmatch = true;
                this.password = '';
                this.checkPassword = '';
              }
            })
      }
    },
    handleFocus(){
      this.unmatch = false;
    },
  },
  mounted() {
    document.body.classList.add('login-body');
  },
  beforeUnmount() {
    document.body.classList.remove('login-body');
  },
};
</script>

<style>
.login-body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: white;
  width: 100%;
  overflow: hidden;
}
</style>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Quicksand:wght@300&display=swap");

.ring {
  position: relative;
  width: 500px;
  height: 500px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.ring i {
  position: absolute;
  inset: 0;
  border: 2px solid white;
  width: 100px;
  height: 200px;
  transition: 0.5s;
}
.ring i:nth-child(1) {
  top: 10%;
  left: 10%;
}
.ring i:nth-child(2) {
  top: 30%;
  left: 30%;
}
.ring i:nth-child(3) {
  top: 50%;
  left: 50%;
}
.ring:hover i {
  border: 6px solid var(--clr);
  filter: drop-shadow(0 0 20px var(--clr));
}
@keyframes animate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
@keyframes animate2 {
  0% {
    transform: rotate(360deg);
  }
  100% {
    transform: rotate(0deg);
  }
}
.login {
  position: absolute;
  width: 300px;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  /* gap: 20px; */
}
.login h2 {
  font-size: 2em;
  color: #212e3e; /* 深灰色 */
  margin-bottom: 20px;
}
.login .inputBx {
  position: relative;
  width: 100%;
  margin-bottom: 20px;
}
.login .inputBx input {
  position: relative;
  width: 100%;
  padding: 12px 20px;
  background: transparent;
  border: 2px solid #212e3e;
  border-radius: 0;
  font-size: 1.2em;
  color: #212e3e;
  box-shadow: none;
  outline: none;
}
.login .inputBx input[type="submit"] {
  width: 100%;
  background: #6D00FF;
  background: linear-gradient(45deg, #6D00FF, #9C47FF);
  border: none;
  cursor: pointer;
  color: white;
}
.login .inputBx input::placeholder {
  color: rgba(33, 46, 62, 0.75);
}

.login .links {
  position: relative;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 20px;
}
.login .links a {
  color: #212e3e;
  text-decoration: none;
}
.login .links em {
  color: rgb(255, 0, 0);
}
</style>
