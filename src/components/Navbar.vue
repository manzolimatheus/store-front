<template>
  <div id="nav" class="sticky-top">
    <OffCanvas :cart="cart" />
    <nav>
      <router-link to="/">Loja</router-link>
      <router-link to="/">Início</router-link>
      <router-link to="/login" v-if="auth === false">Entrar</router-link>
      <router-link to="/about">Sobre</router-link>
      <a
        data-bs-toggle="offcanvas"
        href="#offcanvasExample"
        role="button"
        aria-controls="offcanvasExample"
        @click="UpdateCanvas"
        v-show="auth"
      >
        Carrinho
      </a>
      <router-link to="/profile" v-show="auth">Minha conta</router-link>
      <a href="/" @click="Logout" v-show="auth">Sair</a>
    </nav>
  </div>
</template>

<script>
import OffCanvas from "@/components/OffCanvas.vue";

export default {
  name: "Navbar",
  props: ["key"],
  components: {
    OffCanvas,
  },
  data() {
    return {
      auth: null,
      canvasKey: null,
      cart: []
    };
  },
  methods: {
    Logout() {
      localStorage.removeItem("token");
      localStorage.setItem('cart', JSON.stringify([]))
      this.auth = false;
    },
    UpdateCanvas(){
      this.cart = JSON.parse(localStorage.getItem("cart"))
    }
  },
  mounted() {
    if (localStorage.getItem("token") !== null) {
      this.auth = true;
    } else {
      this.auth = false;
    }
  },
};
</script>

<style scoped>
nav {
  background-color: tomato;
  padding: 1%;
}

nav a {
  color: white;
  text-decoration: none;
  padding-inline: 1%;
}
</style>
