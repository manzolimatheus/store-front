<template>
  <div id="admin" class="container">
    <Container>
      <h3>Olá, {{ user }}</h3>
      <p>Seja bem-vindo ao painel de administração!</p>
      <button class="btn btn-success rounded-pill mt-3">
        + Cadastrar produto
      </button>
    </Container>
    <div class="row">
      <div class="col-md text-center">
      <Container>
        <h3>Usuários cadastrados</h3>
        {{ data.users }}
      </Container>
      </div>
      <div class="col-md text-center">
      <Container>
        <h3>Produtos cadastrados</h3>
        {{ data.products }}
      </Container>
      </div>
      <div class="col-md text-center">
      <Container>
        <h3>Pedidos cadastrados</h3>
        {{ data.orders }}
      </Container>
      </div>
    </div>
  </div>
</template>

<script>
import Container from "@/components/Container.vue";

export default {
  name: "Admin",
  components: {
    Container,
  },
  data() {
    return {
      user: null,
      data: [],
    };
  },
  methods: {
    LoadUser() {
      const token = localStorage.getItem("token");

      if (token !== null) {
        const url = "http://127.0.0.1:8000/api/user";

        fetch(url, {
          method: "POST",
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json",
            Authorization: `Bearer ${token}`,
          },
        })
          .then((response) => response.json())
          .then((response) => {
            this.user = response.name;
          });
      } else {
        this.$router.push("/login");
      }
    },
    async LoadInfo() {
      const token = localStorage.getItem("token");
      const pass = "laravel";

      if (token !== null) {
        const url = `http://127.0.0.1:8000/api/${pass}/admin/`;

        fetch(url)
          .then((response) => response.json())
          .then((response) => {
            this.data = response;
          });
      } else {
        this.$router.push("/login");
      }
    },
  },
  mounted() {
    this.LoadUser();
    this.LoadInfo();
  },
};
</script>

<style>
</style>