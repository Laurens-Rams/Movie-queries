<script setup>

// TODO get how many movies we have 
import { ref, computed, watch, nextTick } from 'vue';
import movies from './assets/movies.json';

const input_xMovies = ref(5)
const nextButtonPressed = ref(0)
const pageNumber = ref(0)
const search = ref("")
const searchActive = ref(false)
const yearsSelection = ref([])


function calculateStars(movie){
  return Math.floor(movie.score / 20)
}

function firstPage(){
  pageNumber.value = 0
}

function nextPage(){
  if (pageNumber.value < pageCount.value) {
    pageNumber.value += 1;
  }
}

function prevPage(){
  if (pageNumber.value > 0) {
    pageNumber.value -= 1;
  }
}

function lastPage(){
  pageNumber.value = pageCount.value;
}

const pageCount = computed(() => {
      return Math.ceil(30 / input_xMovies.value - 1);
})

// THIS I DID NOT DOQ I dont understand the whole next function :(
const uniqueElementsBy = (arr, fn) =>
  arr.reduce((acc, v) => {
    if (!acc.some(x => fn(v, x))) acc.push(v);
    return acc;
}, []);

// from here is all mine again

// combined functions which either displays the regular list or search
function GetTheList() {
  if (search.value === "") {
      searchActive.value = false
      const start = pageNumber.value * input_xMovies.value,
      end = start + input_xMovies.value;

      if (1 == 2){ // ignore this at the moment 
        return this.movies.slice(start, end);
      }else {
        const task_names = movies.filter(movie => movie.year == yearsSelection.value)
        return task_names
      }
  } else if (search.value != ""){
      return this.movies.filter(movie => {
        searchActive.value = true
        return movie.title.toLowerCase().indexOf(this.search.toLowerCase()) > -1
      })
  } else {
    console.log("error")
  }

}

const words = ['Python', 'Javascript', 'Go', 'Java', 'PHP', 'Ruby'];
const result = words.filter(word => word.length < 8);
console.log(result);

const sum = movies.reduce((acc, item) => {
    return acc + parseInt(item.score) / 29 // TODO get how many movies we have 
  }, 0);

// filter genres
function filterBy(movies, sci_fi) {
    let set = new Set(sci_fi.map(({genre}) => genre)); // for faster lookup
    return movies.filter(({genre}) => set.has(genre));
}

const sci_fi = [{genre: 'sci-fi',}, {genre: 'Science Fiction',}];

console.log(filterBy(movies, sci_fi));


  
</script>

<template>
  <h3>Average Score: {{Math.round(sum)}}</h3>
  <div class="search_field">
    <form name="search" @submit.prevent="SearchMovies">
        <input type="text" class="input" placeholder="What are you looking for?" v-model="search"/>
    </form>
  </div>
  <div v-show="searchActive != true" class="list_settings">
    <button  :style="pageNumber !== 0 ? 'opacity: 1' : 'opacity: 0.5'" @click="firstPage" class="next">FIRST</button>
    <button  :style="pageNumber > 0 ? 'opacity: 1' : 'opacity: 0.5'" @click="prevPage" class="next">&larr;</button>
    <button  :style="pageNumber < pageCount ? 'opacity: 1' : 'opacity: 0.5'" @click="nextPage" class="next">&rarr;</button>
    <button  :style="pageNumber !== pageCount ? 'opacity: 1' : 'opacity: 0.5'" @click="lastPage" class="next">LAST</button>
  </div>

  <div v-show="searchActive != true" class="list_settings">
    <input 
      class="xMovies"
      v-model="input_xMovies"
      type="number" 
      placeholder="Enter how many movies you want to see." 
      /><label>Movies per page.</label>
      <p id="pages">Page {{pageNumber}} / {{ pageCount }}</p>
      <div class="multiselectors">
        <div class="selection">
          <select  v-model="yearsSelection" multiple>
              <option v-for="movie in movies" :key="movies.id">
                {{ movie.year }}
              </option>
          </select>
        </div>
        <div class="selection">
          <select  v-model="genreSelection" multiple>
              <option v-for="movie in movies" :key="movies.id">
                {{ movie.genre }}
              </option>
          </select>
        </div>
      </div>
  </div>
    <ul class="all_cards">
        <li v-for="movie in GetTheList()" :key="movies.id">
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
