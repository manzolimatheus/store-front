<template>
  <div id="carousel" class="m-1 w-100 rounded-bottom">
    <div
      id="carouselExampleControls"
      class="carousel slide"
      data-bs-ride="carousel"
    >
      <div class="carousel-inner">
        <div class="carousel-item active">
          <img :src="image01" class="d-block w-100 carousel-image" alt="..." />
        </div>
        <div class="carousel-item">
          <img :src="image02" class="d-block w-100 carousel-image" alt="..." />
        </div>
        <div class="carousel-item">
          <img :src="image03" class="d-block w-100 carousel-image" alt="..." />
        </div>
      </div>
      <button
        class="carousel-control-prev"
        type="button"
        data-bs-target="#carouselExampleControls"
        data-bs-slide="prev"
      >
        <span class="carousel-control-prev-icon" aria-hidden="true"></span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button
        class="carousel-control-next"
        type="button"
        data-bs-target="#carouselExampleControls"
        data-bs-slide="next"
      >
        <span class="carousel-control-next-icon" aria-hidden="true"></span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Carousel",
  props: ["image01", "image02", "image03"],
};
</script>

<style>
.carousel-image {
  height: 300px;
  object-fit: cover;
}
</style>
