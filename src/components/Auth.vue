<template>
  <div id="auth" class="row w-100 bg-white ">
    <div class="col-lg g-0">
      <img :src="image" alt="Imagem em destaque" class="image-auth" />
    </div>
    <div class="col-lg">
      <div class="container p-3">
        <slot></slot>
        <p class="text-danger">
          {{ message }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Auth",
  props: ["image", "message"],
};
</script>

<style scoped>
.image-auth{
  width: 100%;
  height: 100vh;
  object-fit: cover;
}
</style>
