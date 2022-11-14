<script setup>
import { ref, computed, watch } from 'vue';
import MOVIES from './assets/movies.json';

const input_xMovies = ref(5)
const pageNumber = ref(1)
const search = ref('')
const yearsSelected = ref([]);
const genreSelected = ref([]);

const movies = MOVIES
  .map(movie => {
    return Object.assign({}, movie, {
      score: Number(movie.score),
      genre: convertGenre(movie.genre)
    });
  });

watch(input_xMovies, () => {
  pageNumber.value = 1;
});

function calculateStars(movie){
  return Math.floor(movie.score / 20)
}

function firstPage(){
  pageNumber.value = 1;
}

function nextPage(){
    pageNumber.value += 1;
}

function prevPage(){
    pageNumber.value -= 1;
}

function lastPage(){
  pageNumber.value = pageCount.value;
}

const pageCount = computed(() => {
      return Math.ceil(filterMovie.value.length / input_xMovies.value); // get how many movies we have
})

function filterByYears(movie) {
  if (yearsSelected.value.length === 0) return true;
  return yearsSelected.value.includes(movie.year);
}

function filterBySearch(movie){
  if(search.value.length < 2) return true;
  return movie.title.toLowerCase().indexOf(search.value.toLowerCase()) > -1
}

function filterByGenre(movie){
  if (genreSelected.value.length === 0) return true;
  return genreSelected.value.includes(movie.genre);
}

const filterMovie = computed(() => {
  return movies
    .filter(filterBySearch)
    .filter(filterByYears)
    .filter(filterByGenre)
})

const filteredYearsList = computed(() => {
  const uniqueYear = [...new Map(movies.map((y) => [y.year, y])).values()];
  return uniqueYear
})

const showMoviesPerpage = computed(() => {
    const start = (pageNumber.value - 1) * input_xMovies.value
    const end = pageNumber.value * input_xMovies.value;

    return filterMovie.value
    .slice(start, end);
})

const filteredMoviesAv = computed(() => {
  return avergageScoreAll(filterMovie.value);
});

const allMoviesAv = computed(() => {
  return avergageScoreAll(movies);
});

const thisPageAv = computed(() => {
  return avergageScoreAll(showMoviesPerpage.value);
});

function avergageScoreAll(current_list) {
  const sum = current_list
  .map(m => m.score)
  .reduce((a, b) => a + b, 0);
  return (sum / current_list.length) || 0;
}

const filteredGenreList = computed(() => {
  const uniqueGenre = [...new Map(movies.map((y) => [y.genre, y])).values()];
  return uniqueGenre
})

// I tried to make a Dictionary with an Array to make the code cleaner, but it did not work out how I wanted.
// Also I thought about Mapping

function convertGenre(movie){
  // problem with capitals, lowercase and uppercase
  let newMovieList = movie.toLowerCase();
  console.log(newMovieList)

  if (['sci-fi', 'science fiction'].indexOf(newMovieList) >= 0) {
        return 'SCIENCE FICTION'
  } else if (movie.indexOf(newMovieList) >= 0) {
        return movie.toUpperCase()
  }else {
        return newMovieList.toUpperCase()
  }
}

watch(search, (newInput) => {
  if (newInput.length > 1) {
    pageNumber.value = 1;
  }
});

</script>
<template>
  <div class="search_field">
    <form name="search" @submit.prevent="SearchMovies">
        <input type="text" class="input" placeholder="What are you looking for?" v-model="search"/>
    </form>
  </div> 
  <div class="list_settings">
    <button  :disabled="pageNumber === 1" @click="firstPage" class="next">FIRST</button>
    <button  :disabled="pageNumber === 1" @click="prevPage" class="next">&larr;</button>
    <button  :disabled="pageNumber === pageCount" @click="nextPage" class="next">&rarr;</button>
    <button  :disabled="pageNumber === pageCount" @click="lastPage" class="next">LAST</button>
  </div>
  <div class="list_settings">
    <input 
      class="xMovies"
      type="number" 
      v-model="input_xMovies"
      placeholder="Enter how many movies you want to see." 
      /><label>Movies per page.</label>
      <p id="pages">Page {{pageNumber}} / {{ pageCount + 1 }}</p>
      <div class="multiselectors">
        <div class="selection">
          <select  v-model="yearsSelected" multiple>
              <option v-for="movie in filteredYearsList" :key="movies.id">
                {{ movie.year }}
              </option>
          </select>
        </div>
        <div class="selection">
          <select  v-model="genreSelected" multiple>
              <option v-for="movie in filteredGenreList" :key="movies.id">
                {{ movie.genre }}
              </option>
          </select>
        </div>
        <div class="average_labels">
          <h2>Average Score</h2>
          <h3>All Pages: <strong id="star">{{ Math.round((allMoviesAv / 20) * 100) / 100 }} of 5</strong></h3>
          <h3>Current Page: <strong id="star">{{ Math.round(thisPageAv / 20 * 100) / 100 }} of 5</strong></h3>
          <h3>Filters: <strong id="star">{{ Math.round(filteredMoviesAv / 20 * 100) / 100 }} of 5</strong></h3>
        </div>
      </div>
  </div>
    <ul class="all_cards">
        <li v-for="movie in showMoviesPerpage" :key="movies.id">
          <div class="movie_card">
            <div class="picture"><img :src="movie.picture" alt="test"/></div>
              <div class="details">
                <p class="title">{{ movie.title }}</p>
                <p class="year">{{ movie.year }}</p>
                <p class="genre">{{ movie.genre }}</p>
                <div class="score"><p class="stars" v-for="i in calculateStars(movie)">☆</p></div>
              </div>
          </div>
        </li>
        </ul>
</template>
<style scoped>
/* TODO: */
</style>
