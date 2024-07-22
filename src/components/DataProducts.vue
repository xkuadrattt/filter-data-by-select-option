<template>
  <section>
    <select name="filter" id="filter" @change="getName">
      <option v-for="item in categoriesData" :key="item.id" :value="item.name">
        {{ item.name }}
      </option>
    </select>
    <table>
      <thead>
        <tr>
          <th>Id</th>
          <th>Title Product</th>
          <th>Description</th>
          <th>Category Name</th>
          <th>Image</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in filterData" :key="item.id">
          <td>{{ item.id }}</td>
          <td>{{ item.title }}</td>
          <td>{{ item.desc }}</td>
          <td>{{ item.categoryName }}</td>
          <td><img :src="item.img" alt="image" class="img-80" /></td>
        </tr>
      </tbody>
    </table>
  </section>
</template>

<style lang="scss">
.img-80 {
  max-width: 80px;
}
</style>

<script setup>
import axiosInit from "@/api";
import { computed, ref } from "vue";

const tableArray = ref([]);
const categoriesData = ref([]);
const nameSelect = ref("");

const filterData = computed(() => {
  const filterArr = tableArray.value.filter((item) => {
    if (item.categoryName.toLowerCase()) {
      return item.categoryName.toLowerCase() === nameSelect.value.toLowerCase();
    } else {
      return tableArray.value;
    }
  });
  return filterArr;
});

const getName = (e) => {
  nameSelect.value = e.target.value;
  console.log(nameSelect.value);
};

const fetchData = async () => {
  const response = await axiosInit.get("products?limit=0&offset=15");
  const data = response.data;
  const mapData = data.map((item) => {
    return {
      id: item.id,
      title: item.title,
      desc: item.description,
      categoryName: item.category?.name,
      img: item.images[0].slice(2, -2),
    };
  });

  tableArray.value = mapData;
  console.log(data);
};

const dataCategory = async () => {
  const response = await axiosInit.get("categories");
  const data = response.data;
  categoriesData.value = data.map((item) => {
    return {
      name: item.name,
    };
  });
};

fetchData();
dataCategory();
</script>
