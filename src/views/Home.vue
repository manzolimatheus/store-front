<template>
  <div class="container mx-auto mb-3" id="home">
    <img src="https://source.unsplash.com/800x300/?Japan" class="w-100" />
    <Container>
      <div class="loading" v-if="userLoaded === false">
        <div class="spinner-border" role="status">
          <span class="visually-hidden">Loading...</span>
        </div>
      </div>
      <div class="loaded" v-show="userLoaded">
        <div class="row">
          <div class="col-md p-5">
            <h1>Olá, {{ user }}! 👋</h1>
            <p>O que deseja fazer hoje?</p>
          </div>
          <div class="col-md">
            <lottie-player
              src="https://assets7.lottiefiles.com/packages/lf20_3vbOcw.json"
              background="transparent"
              speed="1"
              style="width: 300px; height: 300px"
              loop
              autoplay
            ></lottie-player>
          </div>
        </div>
      </div>
    </Container>
    <div class="row">
      <div class="col-md" v-if="user !== 'Visitante'">
        <router-link to="/profile" class="text-decoration-none text-black">
          <Container class="text-center p-5">
            <h2><ion-icon name="cube-outline"></ion-icon> Pedidos</h2>
            <img src="https://www.pngall.com/wp-content/uploads/5/Cardboard-Box-PNG-Image.png" alt="Caixa de pedidos" class="block-image">
          </Container>
        </router-link>
      </div>
      <div class="col-md" v-if="user !== 'Visitante'">
        <router-link to="/admin" class="text-decoration-none text-black">
          <Container class="text-center p-5">
            <h2><ion-icon name="cog-outline"></ion-icon> Gerenciar</h2>
            <img src="https://image.freepik.com/vetores-gratis/ilustracao-de-um-roda-dentada_53876-6328.jpg" alt="Gerenciar" class="block-image">
          </Container>
        </router-link>
      </div>
    </div>
    <Container>
      <h1><ion-icon name="apps-outline"></ion-icon> Categorias</h1>
      <div class="loading" v-if="tagLoaded === false">
        <div class="spinner-border" role="status">
          <span class="visually-hidden">Loading...</span>
        </div>
        <p v-if="tags.length < 1">Não há categorias para listar.</p>
      </div>
      <div class="loaded" v-show="tagLoaded">
        <div class="row">
          <div class="wrapper">
            <div class="col" v-for="tag in tags" :key="tag.id">
              <Block
                :name="tag.name"
                @click="
                  loadProducts(
                    `http://127.0.0.1:8000/api/laravel/products/tag/${tag.id}`
                  )
                "
              />
            </div>
          </div>
        </div>
      </div>
    </Container>
    <Container>
      <h1><ion-icon name="balloon-outline"></ion-icon> Produtos ({{ products.length }})</h1>
      <div class="loading" v-if="producstsLoaded === false">
        <div class="spinner-border" role="status">
          <span class="visually-hidden">Loading...</span>
        </div>
        <p v-if="tags.length < 1">Não há categorias para listar.</p>
      </div>
      <div v-show="producstsLoaded">
        <div class="row">
          <div class="col-md-3" v-for="product in products" :key="product.id">
            <Card
              :id="product.id"
              :image="product.image"
              :name="product.name"
              :price="product.price"
              :perishable="product.perishable"
              :tag="product.tag"
            />
          </div>
        </div>
        <div class="container text-center" v-if="pages.per_page < pages.total">
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
                <a
                  class="page-link"
                  @click="loadProducts(pages.next_page_url)"
                  >{{ pages.current_page + 1 }}</a
                >
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
        <p v-if="products.length < 1">Não há produtos para listar.</p>
      </div>
    </Container>
  </div>
</template>

<script>
import Container from "@/components/Container.vue";
import Block from "@/components/Block.vue";
import Card from "@/components/Card.vue";

export default {
  name: "Home",
  props: ["key"],
  data() {
    return {
      user: "Visitante",
      tags: [],
      products: [],
      pages: [],
      tagLoaded: false,
      userLoaded: false,
      producstsLoaded: false,
    };
  },
  components: {
    Container,
    Block,
    Card,
  },
  methods: {
    async loadCategories() {
      const pass = "laravel";
      const url = `http://127.0.0.1:8000/api/${pass}/tags`;

      fetch(url)
        .then((response) => response.json())
        .then((response) => {
          this.tags = response;
          this.tagLoaded = true;
        });
    },
    async loadProducts(url) {
      fetch(url)
        .then((response) => response.json())
        .then((response) => {
          this.pages = response;
          this.products = response.data;
          this.producstsLoaded = true;
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
            this.userLoaded = true;
          });
      } else {
        this.userLoaded = true;
      }
    },
  },
  mounted() {
    this.LoadUser();
    this.loadCategories();
    this.loadProducts("http://127.0.0.1:8000/api/laravel/products");
  },
};
</script>

<style scoped>
.wrapper {
  display: flex;
  overflow-x: auto;
  padding: 3%;
}

.block-image{
  width: 200px; height:200px; object-fit: cover;
}
</style>
