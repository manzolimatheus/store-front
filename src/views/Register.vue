<template>
  <div id="register">
    <Auth
      image="https://images.unsplash.com/photo-1551641506-ee5bf4cb45f1?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=884&q=80"
      :message="message"
    >
      <h1>Cadastrar-se</h1>
      <form @submit.prevent="Register()">
        <label for="name">Nome</label>
        <input type="text" class="form-control" v-model="name" required />
        <br />
        <label for="email">Email</label>
        <input type="email" class="form-control" v-model="email" required />
        <br />
        <label for="password">Senha</label>
        <input
          type="password"
          class="form-control"
          v-model="password"
          @input="CheckPass"
          minlength="8"
          required
        />
        <br />
        <label for="confirm_password">Confirme sua senha</label>
        <input
          type="password"
          class="form-control"
          v-model="confirm_password"
          @input="CheckPass"
          minlength="8"
          required
        />
        <br />
        <router-link to="/login" class="text-decoration-none"
          >Já tem uma conta? Entre</router-link
        >
        <br />
        <button
          class="btn btn-success w-100 p-3 mt-3"
          id="button"
          type="submit"
        >
          Cadastrar 
        </button>
      </form>
    </Auth>
  </div>
</template>

<script>
import Auth from "@/components/Auth.vue";

export default {
  name: "Register",
  components: {
    Auth,
  },
  data() {
    return {
      name: null,
      email: null,
      password: null,
      confirm_password: null,
      message: null,
    };
  },
  methods: {
    CheckPass() {
      if (this.password !== this.confirm_password) {
        this.message = "As senhas não coincidem!";
      } else {
        this.message = null;
      }
    },
    async Register() {
      document.querySelector(
        "#button"
      ).innerHTML = `<div class="spinner-border" role="status">
  <span class="visually-hidden">Loading...</span>
</div>`;
      document.querySelector("#button").disabled = true;

      const url = "http://127.0.0.1:8000/api/register";

      const body = {
        name: this.name,
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
          console.log(response);
          this.message = response.message;
          if (response.status === 500) {
            document.querySelector("#button").disabled = false;
            document.querySelector("#button").innerHTML = "Cadastrar";
          } else {
            localStorage.setItem("token", response.access_token);
            this.$router.push("/");
          }
        });
    },
  },
};
</script>

<style></style>
