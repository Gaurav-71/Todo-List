<template>
  <div class="login">
    <div class="login-container" v-if="$store.state.isLoggingIn">
      <div class="login-box">
        <div class="login-icon">
          <img src="../assets/web.svg" alt="signin" />
          <span>Login</span>
        </div>
        <div class="id">
          <input type="text" placeholder="Email ID" v-model="email" />
        </div>
        <div class="password">
          <input type="password" placeholder="Password" v-model="password" />
        </div>
        <div class="submit">
          <button @click="login">Login</button>
        </div>
      </div>
    </div>
    <div class="loading-icon" v-else>
      <img src="../assets/Interwind-1s-200px.svg" alt="loading" style="width: 80px" />
      <p>Verifying user credentials ...</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "Login",
  data() {
    return {
      email: "",
      password: ""
    };
  },
  methods: {
    login() {
      let data = { email: this.email, password: this.password };
      this.$store
        .dispatch("login", data)
        .then(() => {
          this.$router.push("todo");
        })
        .catch(err => {
          alert(err);
        });
      (this.email = ""), (this.password = "");
    }
  },
  mounted() {
    let user = JSON.parse(localStorage.getItem("loggedUser"));
    if (user) {
      this.$store.state.user = user;
      this.$router.push("todo");
    }
  },
  created() {
    if (JSON.parse(localStorage.getItem("loggedUser"))) {
      this.$store.state.isLoggedIn = true;
    }
  }
};
</script>

<style scoped lang="scss">
.login {
  text-align: center;
  .login-container {
    display: flex;
    justify-content: center;
    .login-box {
      border-radius: 1em;
      width: 22rem;
      height: 20rem;
      box-shadow: 0 0 20px black;
      border: 2px solid grey;
      .login-icon {
        text-align: left;
        img {
          width: 15%;
          margin: 1.2rem 0 1.2em 2.3em;
        }
        span {
          font-size: 30px;
          color: orangered;
          position: absolute;
          float: left;
          margin: 1em 0 0 0.5em;
        }
        border-bottom: 1px solid grey;
        margin-bottom: 2.3em;
      }
      .id,
      .password {
        margin: 1.5em 2em 1em 2em;
        input {
          width: calc(100% - 40px);
          color: papayawhip;
          background: none;
          outline: none;
          padding: 0.7rem;
          border-color: orangered;
        }
      }
      .submit {
        width: 100%;
        margin-top: 1.5em;
        button {
          width: 30%;
          font-size: 15px;
          padding: 0.5em;
          border: none;
          outline: none;
          background: none;
          color: orangered;
          box-shadow: 0 0 10px black;
          cursor: pointer;
        }
      }
    }
  }
  .loading-icon {
    margin-top: 1em;
    text-align: center;
    p {
      margin-top: 0;
      color: rgb(211, 50, 50);
      font-size: 1.5rem;
    }
  }
}
</style>