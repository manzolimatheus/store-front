<template>
  <div
    id="block"
    class="rounded text-center m-1"
    :style="{
      backgroundImage: `linear-gradient(rgba(0, 0, 0, 0.527),rgba(0, 0, 0, 0.5)) ,url(${link})`,
    }"
  >
    <span></span>
    <h3 class="text-white">{{ name }}</h3>
    <span></span>
  </div>
</template>

<script>
export default {
  name: "Block",
  props: ["name"],
  computed: {
    link: function () {
      return `https://source.unsplash.com/200x200/?${this.name}`;
    },
  },
};
</script>

<style scoped>
#block {
  width: 200px;
  height: 200px;
  display: grid;
  grid-template-rows: repeat(3, 1fr);
}

#block:hover {
  transform: scale(1.05);
  cursor: pointer;
}
</style>
