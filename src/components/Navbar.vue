<template>
  <div id="nav" class="sticky-top text-center">
    <OffCanvas :cart="cart" />
    <nav>
      <router-link to="/"><img src="/img/logo.png" alt="Logo" style="width:50px; clip-path: circle()"></router-link>
      <router-link to="/"><ion-icon name="home-outline"></ion-icon></router-link>
      <router-link to="/login" v-if="auth === false"><ion-icon name="log-in-outline"></ion-icon></router-link>
      <a
        data-bs-toggle="offcanvas"
        href="#offcanvasExample"
        role="button"
        aria-controls="offcanvasExample"
        @click="UpdateCanvas"
        v-show="auth"
      >
        <ion-icon name="cart-outline"></ion-icon>
      </a>
      <router-link to="/profile" v-show="auth"><ion-icon name="person-circle-outline"></ion-icon></router-link>
      <a href="/" @click="Logout" v-show="auth"><ion-icon name="log-out-outline"></ion-icon></a>
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
      cart: [],
    };
  },
  methods: {
    Logout() {
      localStorage.removeItem("token");
      localStorage.setItem("cart", JSON.stringify([]));
      this.auth = false;
    },
    UpdateCanvas() {
      this.cart = JSON.parse(localStorage.getItem("cart"));
    },
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
  background-color: #e070a5;
  padding: 1%;
}

nav ion-icon, a {
  color: white;
  font-size: 20pt;
  padding-inline: 1%;
}
</style>
