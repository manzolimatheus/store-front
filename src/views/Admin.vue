<template>
  <div id="admin" class="container mb-3">
    <Container>
      <h3>Olá, {{ user }}</h3>
      <p>Seja bem-vindo ao painel de administração!</p>
      <button
        class="btn btn-success rounded-pill mt-3"
        data-bs-toggle="modal"
        data-bs-target="#exampleModal"
      >
        + Cadastrar produto
      </button>
    </Container>
    <div class="row">
      <div class="col-md text-center">
        <Container>
          <h3>Usuários cadastrados</h3>
          <img
            src="https://image.freepik.com/free-vector/user-verification-unauthorized-access-prevention-private-account-authentication-cyber-security-people-entering-login-password-safety-measures_335657-3530.jpg"
            alt="Usuários cadastrados"
            class="block-image"
          />
          <h1>{{ data.users }}</h1>
        </Container>
      </div>
      <div class="col-md text-center">
        <Container>
          <h3>Produtos cadastrados</h3>
          <img
            src="https://image.freepik.com/free-vector/merch-clothing-abstract-concept-vector-illustration-event-apparel-custom-merchandise-products-merch-design-service-branded-print-clothing-merch-maker-online-website-abstract-metaphor_335657-4159.jpg"
            alt="Produtos cadastrados"
            class="block-image"
          />
          <h1>{{ data.products }}</h1>
        </Container>
      </div>
      <div class="col-md text-center">
        <Container>
          <h3>Pedidos cadastrados</h3>
          <img
            src="https://image.freepik.com/free-vector/taking-orders-by-phone-store-contact-center-customers-support-easy-order-fast-delivery-trade-service-call-center-operator-cartoon-character_335657-2564.jpg"
            alt="Pedidos registrados"
            class="block-image"
          />
          <h1>{{ data.orders }}</h1>
        </Container>
      </div>
    </div>
    <Container v-if="products.length > 0">
      <h3>
        <ion-icon name="balloon-outline"></ion-icon> Produtos ({{
          pages.total
        }})
      </h3>
      <span class="text-success">{{ message }}</span>
      <div class="wrapper">
        <table class="table">
          <thead>
            <tr>
              <th scope="col">ID</th>
              <th scope="col">Produto</th>
              <th scope="col">Imagem</th>
              <th scope="col">Preço</th>
              <th scope="col">Categoria</th>
              <th scope="col">Perecível?</th>
              <th scopes="col">Inserido em</th>
              <th scope="col">Ações</th>
            </tr>
          </thead>
          <div class="loading" v-if="producstsLoaded === false">
            <div class="spinner-border" role="status">
              <span class="visually-hidden">Loading...</span>
            </div>
          </div>
          <tbody v-show="productsLoaded">
            <tr v-for="(product, index) in products" :key="product.id">
              <th scope="row">{{ product.id }}</th>
              <td>
                <input
                  type="text"
                  :id="`inputname${product.id}`"
                  :value="product.name"
                  class="form-control"
                  @change="
                    LocalUpdate(`#inputname${product.id}`, 'name', index)
                  "
                />
              </td>
              <td>
                <div class="row">
                  <div class="col-md-3">
                    <img
                      :src="product.image"
                      :alt="`Imagem de ${product.name}`"
                      style="width: 50px; height: 35px; object-fit: cover"
                    />
                  </div>
                  <div class="col-md-9">
                    <input
                      type="text"
                      :id="`inputimage${product.id}`"
                      :value="product.image"
                      class="form-control"
                      @change="
                        LocalUpdate(`#inputimage${product.id}`, 'image', index)
                      "
                    />
                  </div>
                </div>
              </td>
              <td>
                <input
                  type="number"
                  :id="`inputnumber${product.id}`"
                  :value="product.price"
                  min="0"
                  step="0.01"
                  class="form-control"
                  @change="
                    LocalUpdate(`#inputnumber${product.id}`, 'price', index)
                  "
                />
              </td>
              <td>
                <select class="form-select" id="tag" @change="selected(index)">
                  <option :value="product.id_tag" selected>
                    {{ product.tag }}
                  </option>
                  <option
                    v-for="tag in tagsFilter(product.id_tag)"
                    :key="tag.id"
                    :value="tag.id"
                    :id="`option${tag.id}`"
                  >
                    {{ tag.name }}
                  </option>
                </select>
              </td>
              <td>
                <span
                  @click="switchPerishable(index)"
                  :id="`perishable${product.id}`"
                  >{{ product.perishable ? "Sim" : "Não" }}</span
                >
              </td>
              <td>{{ date(product.created_at) }}</td>
              <td>
                <button class="btn btn-danger" @click="Delete(product.id)">
                  X
                </button>
                <button class="btn btn-primary" @click="Edit(product)">
                  🖊
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </Container>
    <Container v-if="pages.per_page < pages.total">
      <div class="container text-center">
        <nav>
          <ul class="pagination">
            <li class="page-item">
              <a
                class="page-link"
                @click="loadProducts(`${pages.links[0].url}`)"
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
              <a class="page-link" @click="loadProducts(pages.next_page_url)">{{
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
                  loadProducts(`${pages.links[pages.current_page + 2].url}`)
                "
                >{{ pages.current_page + 2 }}</a
              >
            </li>
            <li class="page-item">
              <a
                class="page-link"
                @click="loadProducts(`${pages.last_page_url}`)"
                >Última página</a
              >
            </li>
          </ul>
        </nav>
      </div>
    </Container>
    <Modal
      @refresh="loadProducts('http://127.0.0.1:8000/api/laravel/products')"
    />
  </div>
</template>

<script>
import Container from "@/components/Container.vue";
import * as dayjs from "dayjs";
import Modal from "@/components/Modal.vue";

export default {
  name: "Admin",
  components: {
    Container,
    Modal,
  },
  data() {
    return {
      user: null,
      data: [],
      products: [],
      pages: [],
      productsLoaded: false,
      message: null,
      tags: [],
    };
  },
  methods: {
    async loadCategories() {
      const pass = "laravel";
      const url = `http://127.0.0.1:8000/api/${pass}/tags`;

      fetch(url)
        .then((response) => response.json())
        .then((response) => {
          this.tags = response;
        });
    },
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
    async loadProducts(url) {
      fetch(url)
        .then((response) => response.json())
        .then((response) => {
          this.pages = response;
          this.products = response.data;
          this.productsLoaded = true;
          this.LoadInfo();
        });
    },
    date(element) {
      return dayjs(element).format("DD/MM/YYYY");
    },
    selected(index) {
      const select = document.querySelector("#tag");
      let value = select.options[select.selectedIndex].value;
      this.products[index].id_tag = value;
    },
    LocalUpdate(element, attr, index) {
      this.products[index][attr] = document.querySelector(element).value;
    },
    Edit(product) {
      const pass = "laravel";
      const token = localStorage.getItem("token");
      const url = `http://127.0.0.1:8000/api/${pass}/product/${product.id}`;

      const body = {
        name: product.name,
        price: product.price,
        perishable: product.perishable,
        id_tag: product.id_tag,
        image: product.image,
      };

      fetch(url, {
        method: "PUT",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
          Authorization: `Bearer ${token}`,
        },
        body: JSON.stringify(body),
      })
        .then((response) => response.json())
        .then((response) => {
          this.message = response.message;
          this.loadProducts("http://127.0.0.1:8000/api/laravel/products");
          this.LoadInfo();

          setTimeout(() => {
            this.message = null;
          }, 2000);
        });
    },
    async Delete(id) {
      const token = localStorage.getItem("token");
      const pass = "laravel";

      fetch(`http://127.0.0.1:8000/api/${pass}/product/${id}`, {
        method: "DELETE",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
          Authorization: `Bearer ${token}`,
        },
      })
        .then((response) => response.json())
        .then((response) => {
          this.message = response.message;
          if (response.status === 201) {
            this.loadProducts("http://127.0.0.1:8000/api/laravel/products");
          }
          setTimeout(() => {
            this.message = null;
          }, 2000);
        });
    },
    tagsFilter(id) {
      const tagsFilter = this.tags.filter((e) => e.id !== id);
      return tagsFilter;
    },
    switchPerishable(index) {
      this.products[index].perishable = !this.products[index].perishable;
    },
  },
  mounted() {
    this.LoadUser();
    this.LoadInfo();
    this.loadCategories();
    this.loadProducts("http://127.0.0.1:8000/api/laravel/products");
  },
};
</script>

<style>
.block-image {
  width: 200px;
  height: 200px;
  object-fit: cover;
}

.wrapper {
  display: flex;
  overflow-x: auto;
  padding: 3%;
}
</style>
