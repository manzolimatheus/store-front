<template>
  <div id="profile" class="container mb-3 h-100">
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
      <hr />
      <button class="text-danger border-0" @click="deleteAccount">
        Deletar conta
      </button>
    </Container>
    <Container>
      <h4>Meus pedidos ({{ pages.total }})</h4>
    </Container>
    <Container v-for="order in orders" :key="order.id">
      <h5>Pedido #{{ order.id }} - Total: R${{ order.total }}</h5>
      <p>Conteúdo do pedido</p>
      <div class="wrapper">
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
          <tr v-for="(item, index) in JSON.parse(order.order)" :key="item.id">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ item.name }}</td>
            <td>R${{ item.price }}</td>
            <td>{{ item.qtd }}</td>
            <td>R${{ item.price * item.qtd }}</td>
          </tr>
        </tbody>
      </table>
      </div>
      <a class="text-danger text-decoration-none" @click="Delete(order.id)"
        >Excluir pedido</a
      >
    </Container>
    <Container v-if="pages.per_page < pages.total">
      <div class="container text-center">
        <nav>
          <ul class="pagination">
            <li class="page-item">
              <a
                class="page-link"
                @click="fetchOrders(`${pages.links[0].url}`)"
                >Página anterior</a
              >
            </li>
            <li class="page-item active">
              <a class="page-link">{{ pages.current_page }}</a>
            </li>
            <li
              class="page-item"
              v-if="pages.current_page + 1 <= pages.last_page"
            >
              <a class="page-link" @click="fetchOrders(pages.next_page_url)">{{
                pages.current_page + 1
              }}</a>
            </li>
            <li
              class="page-item"
              v-if="pages.current_page + 2 <= pages.last_page"
            >
              <a
                class="page-link"
                @click="
                  fetchOrders(`${pages.links[pages.current_page + 2].url}`)
                "
                >{{ pages.current_page + 2 }}</a
              >
            </li>
            <li class="page-item">
              <a
                class="page-link"
                @click="fetchOrders(`${pages.last_page_url}`)"
                >Última página</a
              >
            </li>
          </ul>
        </nav>
      </div>
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
      pages: [],
    };
  },
  methods: {
    fetchOrders(url) {
      const token = localStorage.getItem("token");

      fetch(url, {
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
          this.orders = response.data;
          this.pages = response;
        });
    },
    async Delete(id) {
      const token = localStorage.getItem("token");
      const pass = "laravel";

      fetch(`http://127.0.0.1:8000/api/${pass}/order/${id}`, {
        method: "DELETE",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
          Authorization: `Bearer ${token}`,
        },
      })
        .then((response) => response.json())
        .then((response) => {
          if (response.status === 201) {
            this.fetchOrders('http://127.0.0.1:8000/api/laravel/myorders');
          }
        });
    },
    async deleteAccount(){
      const token = localStorage.getItem("token");
      console.log(token)

      fetch(`http://127.0.0.1:8000/api/user/`, {
        method: "DELETE",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
          Authorization: `Bearer ${token}`,
        },
        body: JSON.stringify({id: this.data.id}),
      })
        .then((response) => response.json())
        .then((response) => {
          if (response.status === 201){
            localStorage.removeItem("token");
            localStorage.setItem("cart", JSON.stringify([]));
            this.$router.push('/')
          }
        });
    }
  },
  mounted() {
    document.querySelector("body").style = ""
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
          this.fetchOrders('http://127.0.0.1:8000/api/laravel/myorders');
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

<style scoped>
.image-icon {
  width: 80px;
  object-fit: cover;
}

a {
  cursor: pointer;
}

.wrapper {
  display: flex;
  overflow-x: auto;
  padding: 3%;
}
</style>
