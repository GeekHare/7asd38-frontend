<template>
  <div class="wrapper">
    <ProductCard
        v-for="product in products"
        v-bind:key="product.id"
        v-bind:id="product.id"
        v-bind:img-link="product.main_photo_link"
        v-bind:title="product.title"
        v-bind:description="product.description"
        v-bind:price="product.price"
    ></ProductCard>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import ProductCard from '@/components/ProductCard.vue';
import axios from "axios";

@Component({
  components: {
    ProductCard,
  },
})
export default class Home extends Vue {
  products = [];
  created() {
    this.$store.state.loader = true;
    axios.get(process.env.VUE_APP_API_URL.concat('/products'))
        .then(response => {
          this.$store.state.loader = false;
          this.products = response.data;
        })
        .catch(error => {
          console.error(error.response.data);
        });
  }
}
</script>

<style scoped lang="scss">
.wrapper {
  max-width: 1280px;
  margin: 50px auto auto;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  grid-gap: 30px;
}
</style>
