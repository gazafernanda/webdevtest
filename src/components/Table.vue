<template>
  <div>
    <input
      type="text"
      v-model="searchQuery"
      placeholder="Search..."
      @input="handleSearch"
      class="search-bar"
    />
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Title</th>
          <th>Body</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in paginatedData" :key="index">
          <td>{{ item.id }}</td>
          <td>{{ item.title }}</td>
          <td>{{ item.body }}</td>
        </tr>
      </tbody>
    </table>
    <div class="pagination">
      <button :disabled="currentPage === 1" @click="changePage(currentPage - 1)">
        Previous
      </button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button :disabled="currentPage === totalPages" @click="changePage(currentPage + 1)">
        Next
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: "",
      currentPage: 1,
      pageSize: 10,
      tableData: [],
      filteredData: [],
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.filteredData.length / this.pageSize);
    },
    paginatedData() {
      const start = (this.currentPage - 1) * this.pageSize;
      const end = start + this.pageSize;
      return this.filteredData.slice(start, end);
    },
  },
  methods: {
    async fetchData() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts'); 
        const data = await response.json();
        this.tableData = data; 
        this.filteredData = data; 
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    handleSearch() {
      this.currentPage = 1; 
      this.filteredData = this.tableData.filter(item =>
        item.body.toLowerCase().includes(this.searchQuery.toLowerCase()) 
      );
    },
    changePage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page;
      }
    },
  },
  mounted() {
    this.fetchData();
  },
};
</script>

<style scoped>
.search-bar {
  margin-bottom: 10px;
  padding: 5px;
  font-size: 16px;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}
th, td {
  padding: 8px;
  text-align: left;
  border: 1px solid #ddd;
}
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
}
.pagination button {
  padding: 5px 10px;
  margin: 0 5px;
  cursor: pointer;
}
.pagination span {
  margin: 0 10px;
}
</style>
