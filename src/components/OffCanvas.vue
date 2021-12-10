<template>
  <div
    class="offcanvas offcanvas-end"
    tabindex="-1"
    id="offcanvasExample"
    aria-labelledby="offcanvasExampleLabel"
  >
    <div class="offcanvas-header">
      <h5 class="offcanvas-title" id="offcanvasExampleLabel">
        Carrinho ({{ cart.length }})
      </h5>
      <span class="text-danger">{{ message }}</span>
      <button
        type="button"
        class="btn-close text-reset"
        data-bs-dismiss="offcanvas"
        aria-label="Close"
      ></button>
    </div>
    <div class="offcanvas-body">
      <table class="table">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Produto</th>
            <th scope="col">Valor Uni.</th>
            <th scope="col">Quantidade</th>
            <th scope="col">Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in cart" :key="item.id">
            <th scope="row">{{ index }}</th>
            <td>{{ item.name }}</td>
            <td>R${{ item.price }}</td>
            <td>
              <input
                type="number"
                :value="item.qtd"
                class="form-control"
                :id="`input${item.id}`"
                @input="updateValue(`#input${item.id}`, index)"
              />
            </td>
            <td>
              <button class="btn btn-danger" @click="removeItem(index)">
                X
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <hr />
      <h3>Total: R${{ total }}</h3>
      <hr />
      <button class="btn btn-success w-100" @click="SendData" id="button">
        Salvar pedido
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "OffCanvas",
  props: ["cart"],
  data() {
    return {
      canvas_cart: [],
      qtd: 0,
      user: null,
      message: null,
    };
  },
  methods: {
    removeItem(id) {
      this.canvas_cart = this.cart;
      this.canvas_cart.splice(id, 1);
      localStorage.setItem("cart", JSON.stringify(this.canvas_cart));
    },
    updateValue(element, index) {
      let obj = document.querySelector(element).value;
      this.canvas_cart = this.cart;

      let item = {
        id: this.canvas_cart[index].id,
        name: this.canvas_cart[index].name,
        image: this.canvas_cart[index].image,
        price: this.canvas_cart[index].price,
        perishable: this.canvas_cart[index].perishable,
        created_at: this.canvas_cart[index].created_at,
        qtd: obj,
      };

      this.canvas_cart[index] = item;
      console.log(this.canvas_cart);
      localStorage.setItem("cart", JSON.stringify(this.canvas_cart));
    },
    async LoadUser() {
      const token = localStorage.getItem("token");
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
          this.user = response.id;
        });
    },
    async SendData() {
      let body = JSON.parse(localStorage.getItem("cart"));

      if (body.length > 0) {
        document.querySelector(
          "#button"
        ).innerHTML = `<div class="spinner-border" role="status">
  <span class="visually-hidden">Loading...</span>
</div>`;
        document.querySelector("#button").disabled = true;
        const token = localStorage.getItem("token");
        const pass = "laravel";

        body[0].id_user = this.user;
        body[0].total = this.total;

        fetch(`http://127.0.0.1:8000/api/${pass}/orders`, {
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
            this.message = response.message;
            if (response.status === 201) {
              localStorage.setItem("cart", JSON.stringify([]));
              this.$router.push("/profile");
            } else {
              document.querySelector("#button").disabled = false;
              document.querySelector("#button").innerHTML = "Salvar pedido";
              localStorage.setItem("cart", JSON.stringify([]));
              this.canvas_cart = [];
            }

            setTimeout(() => {
              this.message = null;
            }, 2000);
          });
      } else {
        this.message = "O carrinho deve conter items para continuar!";

        setTimeout(() => {
          this.message = null;
        }, 2000);
      }
    },
  },
  computed: {
    total: function () {
      let total = 0;

      this.cart.forEach((element) => {
        total += parseFloat(element.price * element.qtd);
      });

      return total.toFixed(2);
    },
  },
  mounted() {
    this.LoadUser();
  },
};
</script>

<style>
.offcanvas {
  width: 50%;
}

@media (max-width: 768px) {
  .offcanvas {
    width: 100%;
  }
}
</style>
