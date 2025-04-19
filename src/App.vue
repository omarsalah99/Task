<template>
  <main
    class="min-h-screen w-full bg-gray-900 text-white flex flex-col items-center justify-center relative"
  >
    <div class="w-4/6 z-10">
      <div class="text-white p-4 rounded mb-6 flex justify-between">
        <h2>
          Total Movies: {{ movies.length }} | Average Rating:
          {{ averageRating.toFixed(1) }}
        </h2>
        <div>
          <button
            class="text-white bg-[#2BA9CA] hover:bg-[#087A96] focus:ring-4 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2"
            @click="clearRatings"
          >
            Remove Rating
          </button>
          <button
            @click="showForm = true"
            class="text-white bg-[#2BA9CA] hover:bg-[#087A96] focus:ring-4 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2"
          >
            Add
          </button>
        </div>
      </div>
      <div class="grid sm:grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6">
        <MovieCard
          v-for="movie in movies"
          :key="movie.id"
          :movie="movie"
          @rated="updateRating"
          @deleteMovie="deleteMovie"
          @editMovie="openEditPopup"
        />
      </div>
    </div>
    <div
      v-if="showForm"
      class="fixed inset-0 flex items-center justify-center z-50 backdrop-blur-sm"
    >
      <div
        class="bg-gray-900 text-white rounded-2xl shadow-2xl p-8 w-full max-w-md transition-transform scale-100"
      >
        <div class="flex flex-col gap-3">
          <h2>Name</h2>
          <input
            v-model="newMovie.name"
            type="text"
            class="p-2 border rounded bg-gray-800 text-white"
          />
          <h2>Description</h2>
          <textarea
            v-model="newMovie.description"
            class="p-2 border rounded bg-gray-800 text-white"
          ></textarea>
          <h2>Image</h2>
          <input
            v-model="newMovie.image"
            type="text"
            class="p-2 border rounded bg-gray-800 text-white"
          />
          <h2>Genres</h2>
          <div class="grid grid-cols-2 gap-2 mb-4">
            <label
              v-for="genre in genresOptions"
              :key="genre"
              class="flex items-center space-x-2"
            >
              <input
                type="checkbox"
                :value="genre"
                v-model="newMovie.genres"
                class=" h-5 w-5 text-indigo-600"
              />
              <span>{{ genre }}</span>
            </label>
          </div>
          <label class="flex items-center gap-2">
            <input v-model="newMovie.inTheaters" type="checkbox" />
            In Theaters
          </label>
          <div class="flex justify-between mt-4">
            <button
              @click="showForm = false"
              class="bg-gray-600 text-white px-4 py-2 rounded hover:bg-gray-500"
            >
              Cancel
            </button>
            <button
              @click="addMovie"
              class="bg-[#2BA9CA] text-white px-4 py-2 rounded hover:bg-[ #087A96]"
            >
              Create
            </button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>
<script setup>
import { ref, onMounted, computed } from "vue";
import axios from "axios";
import Swal from "sweetalert2";
import MovieCard from "./components/MovieCard.vue";
// array to Data storage
const movies = ref([]); 
// variable to control the popupform
const showForm = ref(false); 
// variable to show the genres for new film
const genresOptions = ["Action", "Drama", "Comedy", "Crime"]; 
// array to save new film
const newMovie = ref({
  id: "",
  name: "",
  description: "",
  image: "",
  genres: [],
  inTheaters: false,
  rating: 0,
});
// It saves data from the JSON file when mounted
onMounted(async () => {
  const response = await axios.get("http://localhost:5173/Project/Task/movie.json"); 
  movies.value = response.data.items; 
});
// update rating for film
const updateRating = (id, rating) => {
  const movie = movies.value.find((m) => m.id === id); 
  if (movie) {
    movie.rating = rating; 
  }
};
// compute average rating and show in header
const averageRating = computed(() => {
  const ratedMovies = movies.value.filter((m) => m.rating > 0); 
  const total = ratedMovies.reduce((sum, m) => sum + m.rating, 0); 
  return ratedMovies.length ? total / ratedMovies.length : 0; 
});
// array to added new film
const addMovie = () => {
  if (
    newMovie.value.name &&
    newMovie.value.description &&
    newMovie.value.image
  ) {
    const movieToAdd = { ...newMovie.value, id: movies.value.length + 1 }; 
    movies.value.push(movieToAdd); // push new film to array
    showForm.value = false; // close form
    // return array to default
    newMovie.value = {
      id: "",
      name: "",
      description: "",
      image: "",
      genres: [],
      inTheaters: false,
      rating: 0,
    };
  } else {
    //alert when user not fill all fields
    Swal.fire({
      icon: "warning", 
      title: "Oops...",
      text: "Please Fill All Fields!", 
    }); 
  }
};
// to delete movie from icon
const deleteMovie = (id) => {
  // Filters the movies list and removes the movie with the given id
  movies.value = movies.value.filter((movie) => movie.id !== id);
};
// Define a function to clear the ratings of all movies
const clearRatings = () => {
  // Loop through each movie in the movies list and Reset the rating of the current movie to 0
  movies.value.forEach((movie) => {
    movie.rating = 0;
  });
};
</script>
