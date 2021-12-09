<template>
  <div id="profile" class="container mb-3">
    <Container>
      <div class="row w-100">
        <div class="col-lg-1">
          <img
            src="https://cdn-icons-png.flaticon.com/512/149/149071.png"
            alt="Usuário"
            class="image-icon p-3"
          />
        </div>
        <div class="col-lg-11">
          <strong>{{ data.name }}</strong>
          <p class="text-muted">{{ data.email }}</p>
          <p>Usuário desde {{ date }}</p>
        </div>
      </div>
    </Container>
    <Container>
      <h4>Meus pedidos ({{orders.length}})</h4>
    </Container>
    <Container v-for="order in orders" :key="order.id">
      <h5>Pedido #{{order.id}}</h5>
      <p>Conteúdo do pedido</p>
      <table class="table">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Produto</th>
            <th scope="col">Valor Uni.</th>
            <th scope="col">Quantidade</th>
            <th scoped="cool">Total</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <th scope="row">{{JSON.parse(order.order)[0].id}}</th>
            <td>{{JSON.parse(order.order)[0].name}}</td>
            <td>R${{JSON.parse(order.order)[0].price}}</td>
            <td>{{JSON.parse(order.order)[0].qtd}}</td>
            <td>R${{JSON.parse(order.order)[0].price * JSON.parse(order.order)[0].qtd}}</td>
          </tr>
        </tbody>
      </table>
      <a class="text-danger" @click="Delete(order.id)">Excluir pedido</a>
    </Container>
  </div>
</template>

<script>
import Container from "@/components/Container.vue";
import * as dayjs from "dayjs";

export default {
  name: "Profile",
  components: {
    Container,
  },
  data() {
    return {
      data: [],
      orders: [],
      pages: []
    };
  },
  methods: {
    fetchOrders() {
      const token = localStorage.getItem("token");
      const pass = "laravel";

      fetch(`http://127.0.0.1:8000/api/${pass}/myorders`, {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
          Authorization: `Bearer ${token}`,
        },
        body: JSON.stringify({ id: this.data.id }),
      })
        .then((response) => response.json())
        .then((response) => {
          this.orders = response.data
          this.pages = response
        });
    },
    async Delete(id){
      const token = localStorage.getItem("token");
      const pass = "laravel";

      fetch(`http://127.0.0.1:8000/api/${pass}/order/${id}`, {
        method: "DELETE",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
          Authorization: `Bearer ${token}`,
        }
      })
        .then((response) => response.json())
        .then((response) => {
          if (response.status === 201){
            this.fetchOrders()
          }
        });
    }
  },
  mounted() {
    document.querySelector("body").style.overflow = 'scroll';
    document.querySelector("body").style.paddingRight = 0;
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
          this.data = response;
          this.fetchOrders()
        });
    } else {
      this.$router.push("/login");
    }
  },
  computed: {
    date: function () {
      return dayjs(this.data.created_at).format("DD/MM/YYYY");
    },
  },
};
</script>

<style>
.image-icon {
  width: 80px;
  object-fit: cover;
}
</style>
