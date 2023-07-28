<template>
  <div>
    <h2>Список товарів</h2>
    <label for="category">Оберіть категорію:</label>
    <select v-model="selectedCategory">
      <option value="">Всі категорії</option>
      <option v-for="category in categories" :value="category" :key="category">
        {{ category }}
      </option>
    </select>
    <section class="tovar_wrap">
      <nav class="tovar_item" v-for="product in paginatedProducts" :key="product.id">
        <div>
          <img :src="product.image" :alt="product.title" />
        </div>
        <div class="tovar_name">
          {{ product.title }}
        </div>
        <div class="tovar_price">
          Ціна - {{ product.price }} грн
        </div>
        <div class="tovar_price">
          Рейтинг - {{ product.rating.rate }}
        </div>
      </nav>
    </section>
    <nav v-if="totalPages > 1">
      <div class="pagination">
        <button
          v-for="page in totalPages"
          :key="page"
          @click="currentPage = page"
          :class="{ active: currentPage === page }"
        >
          {{ page }}
        </button>
      </div>
    </nav>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      products: [],
      selectedCategory: '',
      categories: [],
      currentPage: 1,
      itemsPerPage: 6,
    };
  },
  created() {
    this.fetchProducts();
  },
  methods: {
    fetchProducts() {
      axios
        .get('https://fakestoreapi.com/products')
        .then((response) => {
          this.products = response.data;
          this.updateCategories();
        })
        .catch((error) => {
          console.error('Помилка під час отримання даних:', error);
        });
    },
    updateCategories() {
      this.categories = [
        ...new Set(this.products.map((product) => product.category)),
      ];
    },
  },
  computed: {
    filteredProducts() {
      return this.selectedCategory
        ? this.products.filter(
            (product) => product.category === this.selectedCategory
          )
        : this.products;
    },
    paginatedProducts() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.filteredProducts.slice(startIndex, endIndex);
    },
    totalPages() {
      return Math.ceil(this.filteredProducts.length / this.itemsPerPage);
    },
  },
};
</script>

<style>

img{
  max-width: 100px;
  height: 120px;
}
.tovar_wrap{
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
}
.tovar_item{
  width: 25%;
  padding: 20px;
  border-radius: 20px;
  border: 2px solid;
  margin: 20px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;

}
.tovar_name{
  text-align: center;
  margin-bottom: 10px;
  font-size: 18px;
  font-weight: 700;
}
.pagination{
  display: flex;
  justify-content: center;
}
.pagination button {
  border-style: none;
  background-color: #333;
  color: #fff;
  margin: 20px;
  width: 30px;
  height: 30px;
  cursor: pointer;
  
}
</style>
