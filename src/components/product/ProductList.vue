<template>
  <h6>Listado de Productos</h6>
  <div class="product-list">
    <div class="product-grid">
      <div class="product-item" v-for="item in products" :key="item.id">
        <ProductItem :product="item" />
      </div>
    </div>
  </div>
</template>

<style>
.product-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 20px;
}
</style>

<script>
import ProductItem from 'src/components/product/ProductItem.vue'

export default {
  name: 'ProductList',
  components: {
    ProductItem,
  },
  data() {
    return {
      products: [],
    }
  },
  mounted() {
    this.loadProducts()
  },
  methods: {
    loadProducts() {
      // Lógica para cargar productos
      let endpointURL = '/api/product'
      let token = localStorage.getItem('token')
      this.$api
        .get(endpointURL, {
          headers: {
            Authorization: `Bearer ${JSON.parse(token)}`,
          },
        })
        .then((response) => {
          this.products = response.data
        })
        .catch((error) => {
          console.error('Error al cargar productos: ', error)
        })
    },
  },
}
</script>
