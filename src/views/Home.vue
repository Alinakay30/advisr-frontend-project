<template>
  <div id="app">
    <h1 class="p-3 text-center">Business List</h1>
    <input
      name="query"
      v-model="search"
      placeholder="Filter by business name"
      class="form-control"
      @keyup="sortByName" />

    <table class="table table-striped table-hover">
      <thead>
        <tr>
          <th @click="sortBy('name')">Name of Business</th>

          <th @click="sortBy('category')">Category of Business</th>

          <th @click="sortBy('numCampaigns')">Number of Campagins</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="business in filteredList" :key="business.name">
          <td>
            <router-link
              :to="{ name: 'About', params: { name: business.name } }" >
              {{ business.name }}
            </router-link>
          </td>
          <td>{{ business.category }}</td>
          <td>{{ business.numCampaigns }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>


<script>
import jsonData from "@/business-list.json";

export default {
  name: "Home",
  el: "#app",
  data() {
    return {
      list: jsonData,

      reverse: false,
      sortKey: null,
      search: "",
    };
  },

  methods: {
    sortBy: function (sortKey) {
      
      this.list.sort(function (a, b) {

        if (typeof a[sortKey] === "number") {
          return a[sortKey] - b[sortKey];
        }

        var a2 = a[sortKey];
        var b2 = b[sortKey];
        return a2 < b2 ? -1 : a2 > b2 ? 1 : 0;
      });

      this.reverse = this.sortKey === sortKey ? !this.reverse : false;
      if (this.reverse) this.list.reverse();
      this.sortKey = sortKey;
    },

    sortByName: function () {
      return this.list.filter((row) => {
        if (String(row).toLowerCase().indexOf(this.search) !== -1) {
          return row;
        }
      });
    },
  },

  computed: {
    filteredList: function () {
      return this.list.filter((row) => {
        for (var key in row) {
          if (String(row[key]).toLowerCase().indexOf(this.search) !== -1) {
            return true;
          }
          return false;
        }
      });
    },
  },
};
</script>


<style scoped>
.form-control {
  width: 45%;
  margin-right: auto;
  margin-left: auto;
  margin-top: 10px;
}

.table {
  border: 1px solid #c9c9c9;
  margin-top: 40px;
  text-align: center;
  border-spacing: 15px;
  margin-left: auto;
  margin-right: auto;
  width: auto;
}
</style>