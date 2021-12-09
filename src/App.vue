<template>
  <Navbar :key="$route.fullPath" />
  <router-view />
  <Footer />
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import Footer from "@/components/Footer.vue";

export default {
  components: {
    Navbar,
    Footer,
  },
  mounted(){
    if (localStorage.getItem('cart') === null){
      const list = []
      
      localStorage.setItem('cart',JSON.stringify(list))
    }
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Oxygen&display=swap");

* {
  padding: 0;
  margin: 0;
  font-family: "Oxygen", sans-serif;
}

body {
  background-color: rgb(226, 226, 226);
}
/* Scrollbar */
/* width */
::-webkit-scrollbar {
  width: 10px;
}
/* Track */
::-webkit-scrollbar-track {
  background: #f1f1f1;
}
/* Handle */
::-webkit-scrollbar-thumb {
  background: #bbbbbb;
  border-radius: 10px;
}
/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>
