<template>
  <div class="product-page">
    <div class="product-filter q-ml-md q-mr-xl">
      <ProductFilter @categoryChanged="updateProductCategory" />
    </div>

    <div class="product-list">
      <ProductList :categoria-filtrada="categorySelected" />
    </div>
  </div>
</template>

<style>
.product-page {
  display: flex;
  justify-content: space-between;
}
.product-filter {
  width: 25%;
}

.product-list {
  width: 75%;
}
</style>

<script>
import ProductFilter from 'src/components/product/ProductFilter.vue'
import ProductList from 'src/components/product/ProductList.vue'

export default {
  name: 'ProductPage',
  components: {
    ProductFilter,
    ProductList,
  },
  data() {
    return {
      categorySelected: null,
    }
  },
  methods: {
    updateProductCategory(category) {
      console.log('Categoría seleccionada:', category)
      // Aquí puedes agregar la lógica para filtrar los productos según la categoría seleccionada
      this.categorySelected = category
    },
  },
}
</script>
