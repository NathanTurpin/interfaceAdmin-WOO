<template>
  <div id="app">
    <div class="login-page">
      <transition name="fade">
        <div v-if="!registerActive" class="wallpaper-login"></div>
      </transition>
      <div class="wallpaper-register"></div>

      <div div class="container">
        <div class="row">
          <div class="col-lg-4 col-md-6 col-sm-8 mx-auto">
            <div
              v-if="!registerActive"
              class="card login"
              v-bind:class="{ error: emptyFields }"
            >
              <h1>Connexion</h1>
              <form class="form-group">
                <input
                  v-model="username"
                  type="username"
                  class="form-control"
                  placeholder="username"
                  required
                />

                <input
                  v-model="passwordLogin"
                  type="password"
                  class="form-control"
                  placeholder="Password"
                  required
                />
                <button
                  type="button"
                  class="btn btn-primary"
                  @click="doLogin"
                >Se connecter </button>
                
                
              </form>
            </div>

           
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      registerActive: false,
      username: "",
      passwordLogin: "",
      usernameReg: "",
      emailReg: "",
      passwordReg: "",
      confirmReg: "",
      emptyFields: false,
      token: ""
    };
  },
  mounted() {
  },
  methods: {
    async doLogin() {
      if (this.username === "" || this.passwordLogin === "") {
        this.emptyFields = true;
      } else {
        const response = await axios.post(
          "http://applicommande.local/wp-json/jwt-auth/v1/token",
          {
            username: this.username,
            password: this.passwordLogin,
          }
        );
    localStorage.setItem("token", response.data.token)
    localStorage.setItem("username",response.data.user_nicename)
    localStorage.setItem("role",response.data.role)
         return  this.verifLogin() ;
      }
    },
     verifLogin(){
       let token = localStorage.getItem('token');
       let role = localStorage.getItem('role')
      if(token && role === 'administrator') {
              window.location.href = "/commandes";

      }else{
        alert('non')
      }
    }}
};
</script>

<style scoped>
p {
  line-height: 1rem;
}

.card {
  padding: 20px;
}

.form-group input {
  margin-bottom: 20px;
}

.login-page {
  align-items: center;
  display: flex;
  height: 100vh;
}

.login-page .fade-enter-active,
.login-page .fade-leave-active {
  transition: opacity 0.5s;
}
.login-page .fade-enter,
.login-page .fade-leave-to {
  opacity: 0;
}

.login-page h1 {
  margin-bottom: 1.5rem;
}

.error {
  animation-name: errorShake;
  animation-duration: 0.3s;
}

@keyframes errorShake {
  0% {
    transform: translateX(-25px);
  }
  25% {
    transform: translateX(25px);
  }
  50% {
    transform: translateX(-25px);
  }
  75% {
    transform: translateX(25px);
  }
  100% {
    transform: translateX(0);
  }
}
</style>