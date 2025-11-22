<template>
  <h6>Filtros</h6>
  <div class="category-filter">
    <q-select
      label="Categoria"
      v-model="categorySelected"
      :options="categories"
      option-value="id"
      option-label="description"
      emit-value
      map-options
      @update:model-value="onChange"
    />
  </div>
</template>

<style></style>

<script>
export default {
  name: 'ProductFilter',
  data() {
    return {
      categories: [],
      categorySelected: null,
      minPrice: 0,
      maxPrice: 0,
    }
  },
  mounted() {
    this.loadCategories()
  },
  methods: {
    onChange(value) {
      this.$emit('categoryChanged', value)
    },
    loadCategories() {
      // Lógica para cargar categorías
      let endpointURL = '/api/category'
      let token = localStorage.getItem('token')
      this.$api
        .get(endpointURL, {
          headers: {
            Authorization: `Bearer ${JSON.parse(token)}`,
          },
        })
        .then((response) => {
          this.categories = response.data
        })
        .catch((error) => {
          console.error('Error al cargar categorías: ', error)
        })
    },
  },
}
</script>
