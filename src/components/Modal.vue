<template>
  <div
    class="modal fade"
    id="exampleModal"
    tabindex="-1"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
  >
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">
            Cadastrar novo produto
          </h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="SendData" id="form">
            <label for="name"
              ><ion-icon name="text-outline"></ion-icon> Nome do produto</label
            >
            <input
              type="text"
              class="form-control"
              v-model="name"
              id="name"
              required
            />
            <br />
            <div class="row">
              <div class="col-md-8">
                <label for="image"
                  ><ion-icon name="image-outline"></ion-icon> Imagem do
                  produto</label
                >
                <input
                  type="text"
                  class="form-control"
                  v-model="image"
                  id="image"
                  required
                />
              </div>
              <div class="col-md-4">
                <img
                  :src="
                    image ||
                    'https://www.signfix.com.au/wp-content/uploads/2017/09/placeholder-600x400.png'
                  "
                  alt="Imagem"
                  class="image rounded"
                />
              </div>
            </div>
            <br />
            <label for="price"
              ><ion-icon name="pricetags-outline"></ion-icon> Preço</label
            >
            <input
              type="number"
              min="0"
              step="0.01"
              class="form-control"
              v-model="price"
              id="price"
            />
            <br />
            <label for="tag"
              ><ion-icon name="pricetag-outline"></ion-icon> Categoria do
              produto</label
            >
            <select class="form-select" id="tag" @change="selected($event)">
              <option v-for="tag in tags" :key="tag.id" :value="tag.id">
                {{ tag.name }}
              </option>
            </select>
            <br />
            <label
              ><ion-icon name="fast-food-outline"></ion-icon> O produto é
              perecível?</label
            >
            <div class="form-check">
              <input
                type="radio"
                v-model="perishable"
                value="0"
                name="perishable"
                class="form-check-input"
                id="opt1"
              />
              <label for="opt1" class="form-check-label">Não</label>
              <br />
              <input
                type="radio"
                v-model="perishable"
                value="1"
                name="perishable"
                class="form-check-input"
                id="opt2"
              />
              <label for="opt2" class="form-check-label">Sim</label>
            </div>
          </form>
        </div>
        <div class="modal-footer">
          <button
            type="button"
            class="btn btn-secondary"
            data-bs-dismiss="modal"
          >
            Fechar
          </button>
          <button
            type="submit"
            class="btn btn-success"
            @click="SendForm"
            data-bs-dismiss="modal"
          >
            Salvar
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Modal",
  data() {
    return {
      name: null,
      price: null,
      image: null,
      id_tag: 1,
      perishable: 0,
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
    selected(event) {
      this.id_tag = event.target.value;
    },
    SendForm() {
      if (
        this.name === null ||
        this.price === null ||
        this.image === null ||
        this.id_tag === null ||
        this.perisable === null
      ) {
        alert("Preencha todos os dados para cadastrar um produto!");
        this.name = null;
        this.price = null;
        this.perishable = 0;
        this.id_tag = 1;
        this.image = null;
      } else {
        this.SendData();
      }
    },
    async SendData() {
      const pass = "laravel";
      const token = localStorage.getItem("token");
      const url = `http://127.0.0.1:8000/api/${pass}/products`;

      const body = {
        name: this.name,
        price: this.price,
        perishable: this.perishable,
        id_tag: this.id_tag,
        image: this.image,
      };

      fetch(url, {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
          Authorization: `Bearer ${token}`,
        },
        body: JSON.stringify(body),
      })
        .then((response) => response.json())
        .then((response) => {
          if (response.status === 201) {
            this.name = null;
            this.price = null;
            this.perishable = 0;
            this.id_tag = 1;
            this.image = null;
            alert(response.message);
            this.$emit("refresh");
          } else {
            alert(response.message);
          }
        });
    },
  },
  mounted() {
    this.loadCategories();
  },
};
</script>

<style scoped>
.image {
  width: 100px;
  height: 100px;
  object-fit: cover;
}
</style>
