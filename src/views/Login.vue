<template>
  <div id="login">
    <Auth
      image="https://images.unsplash.com/photo-1580913428735-bd3c269d6a82?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=870&q=80"
      :message="message"
    >
      <h1>Entrar</h1>
      <form @submit.prevent="Login">
        <label for="email">Email</label>
        <input
          type="email"
          id="email"
          v-model="email"
          class="form-control"
          required
        />
        <br />
        <label for="password">Senha</label>
        <input
          type="password"
          id="password"
          v-model="password"
          class="form-control"
          required
          minlength="8"
        />
        <br />
        <router-link to="/register" class="text-decoration-none">Não tem uma conta? cadastre-se.</router-link>
        <br>
        <button type="submit" class="btn btn-success w-100 p-3 mt-3" id="button">
          Entrar
        </button>
      </form>
    </Auth>
  </div>
</template>

<script>
import Auth from "@/components/Auth.vue";

export default {
  name: "Login",
  components: {
    Auth,
  },
  data() {
    return {
      email: null,
      password: null,
      message: null,
    };
  },
  methods: {
    async Login() {
      document.querySelector(
        "#button"
      ).innerHTML = `<div class="spinner-border" role="status">
  <span class="visually-hidden">Loading...</span>
</div>`;
      document.querySelector("#button").disabled = true;

      const url = "http://127.0.0.1:8000/api/login";

      const body = {
        email: this.email,
        password: this.password,
      };

      fetch(url, {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
        },
        body: JSON.stringify(body),
      })
        .then((response) => response.json())
        .then((response) => {
          this.message = response.message;
          if (response.status === 200) {
            localStorage.setItem("token", response.access_token);
            this.$router.push("/");
          } else {
            document.querySelector("#button").disabled = false;
            document.querySelector("#button").innerHTML = "Entrar";
          }
        });
    },
  },
};
</script>

<style></style>
