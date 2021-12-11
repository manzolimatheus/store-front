<template>
  <div class="card border-0 w-100">
    <img :src="image" class="card-img-top" :alt="`Imagem de ${name}`" />
    <div class="card-body">
      <h5 class="card-title">
        <p class="text-truncate">{{ name }}</p>
        <p class="badge bg-success">R${{ price }}</p>
      </h5>
      <p class="card-text text-muted">
        {{ perishable ? "Perecível" : "Não Perecível" }} - {{ tag }}
      </p>
      <button
        class="btn btn-success rounded w-100"
        @click="AddCart(`#button${id}`)"
        :id="`button${id}`"
      >
        + Adicionar ao carrinho
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Card",
  props: ["id", "name", "image", "price", "perishable", "tag"],
  methods: {
    AddCart(id) {
      if (localStorage.getItem("token") == null) {
        this.$router.push("/login");
      } else {
        let list = JSON.parse(localStorage.getItem("cart"));
        const element = list.filter((obj) => obj.id == this.id);

        const item = {
          id: this.id,
          name: this.name,
          image: this.image,
          price: this.price,
          perishable: this.perishable,
          created_at: this.created_at,
          qtd: element.length > 0 ? element[0].qtd + 1 : 1,
        };

        if (element.length > 0) {
          list[list.indexOf(element[0])] = item;
          localStorage.setItem("cart", JSON.stringify(list));
        } else {
          list.push(item);
          localStorage.setItem("cart", JSON.stringify(list));
        }

        document.querySelector(id).innerHTML = "Produto adicionado!";

        setTimeout(() => {
          document.querySelector(id).innerHTML = "+ Adicionar ao carrinho";
        }, 1000);
      }
    },
  },
};
</script>

<style>
.card-img-top {
  width: 100%;
  height: 200px;
  object-fit: cover;
}
</style>
