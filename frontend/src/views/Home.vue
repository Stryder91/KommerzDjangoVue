<template>
  <div class="home">
    <section class="hero-body has-text-centered">
      <p class="title mb-6">
        Welcome to Mei-hua shop
      </p>
      <p class="subtitle">
        The best jacket store online
      </p>
    </section>

    <div class="columns is-multiline">
      <div class="column is-12">
        <h2 class="is-size-2 has-text-centered">Latest products</h2>
      </div>

      <ProductBox 
        v-for="product in latestProducts"
        v-bind:key="product.id"
        v-bind:product="product"
      />
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import ProductBox from '@/components/ProductBox';

export default {
  components: { ProductBox },
  name: 'Home',
  data() {
    return {
      latestProducts:[]
    }
  },
  component: {
    ProductBox
  },
  mounted() {
    this.getLatestProducts();
    document.title = 'Home | Meihua Shop';
  },
  methods: {
      async getLatestProducts() {
      this.$store.commit('setIsLoading', true); 
      await axios.get('/api/v1/latest-products/')
      .then(res => {
        this.latestProducts = res.data;
      })
      .catch(error => {
        console.log(error)
      });
      this.$store.commit('setIsLoading', false); 
    }
  }
}
</script>