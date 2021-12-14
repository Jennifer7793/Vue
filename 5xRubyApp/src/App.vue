<script setup>
  import { ref, computed, watch } from 'vue'
  const movieData = ref([]);
  //單頁筆數
  const CountOfPage = 12;

  //目前頁數
  const currentPage = ref(1);
  const pageStart = computed(() => (currentPage.value - 1) * CountOfPage);
  const pageEnd = computed(() => currentPage.value * CountOfPage);

  const totalPage = computed(() => Math.ceil(filteredMovieData.value.length / CountOfPage))
  const slicedMovieData = computed(() => filteredMovieData.value.slice(pageStart.value, pageEnd.value));

  const searchText = ref('');
  const filteredMovieData = computed(() => {
    return searchText.value !== ''
      ?movieData.value.filter (d => d.title.includes(searchText.value))
      : movieData.value;
    });

  watch(() => searchText.value, (val, oldvalue) => {
    currentPage.value = 1;
  });

  fetch('https://top-250-movies.herokuapp.com/api/v1/movies/top')
    .then(d => d.json())
    .then(res => {
      movieData.value = res;
    });
</script>

<template>
   <div class="navbar">
    <div class='nav-container'>
      <div>
        <a class="logo" href="#">5x<span>Movies</span></a>
      </div>
      <form class="search-field">
        <input v-model="searchText" type="text" class="search-term" placeholder="請搜尋...">
        <button type="submit" class="search-button">搜尋</button>
      </form>
    </div>
  </div>

  <div class='content'>
    <table class="styled-table">
      <thead>
        <tr>
          <th>排名</th>
          <th>片名</th>
          <th>年份</th>
          <th>導演</th>
          <th>主要演員</th>
          <th>評分</th>
        </tr>
      </thead>
      <tbody>
        
        <tr v-for="movie in slicedMovieData" :key="movie">
          <td>{{ movie.rank }}</td>
          <td><a v-bind:href="movie.link">{{ movie.title }}</a></td>
          <td>{{ movie.year }}</td>
          <td>{{ movie.director }}</td>
          <td>{{ movie.main_stars }}</td>
          <td>{{ movie.rank }}</td>
        </tr> 
       
      </tbody>
    </table>

    <div v-if="filteredMovieData.length === 0">Sorry, can't find the movie</div>

    <div class="pagination-container">
      
      <span v-if="currentPage > 1" @click = "currentPage--" 
        class="back-page">＜</span>
      <div class="pagination">
        <span v-for="i in totalPage" 
        :key="i"
        :class="{
          'active': i === currentPage
        }"
        @click="currentPage += i"
        >{{ i }}</span>
      </div>
      <span v-if="currentPage < totalPage" @click = "currentPage++" 
        class="next-page">＞</span>
      <div class="pagination-counter">
      </div> 
     
    </div>
  </div>
  <footer>
    <p>5xCampus © 2021 Ruby on Rails</p>
  </footer>
</template>

<style src="./style/main.css"></style>
