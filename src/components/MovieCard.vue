<template>
  <div
    class="bg-white text-black rounded shadow overflow-hidden group relative"
  >
    <div
      class="absolute top-2 right-2 flex items-center justify-center rounded-full w-10 h-10 text-white font-bold"
    >
      <font-awesome-icon
        :icon="['fas', 'star']"
        :class="movie.rating > 0 ? 'text-yellow-400' : 'text-gray-400'"
        size="2xl"
      />
      <span v-if="movie.rating > 0" class="absolute text-white text-sm">
        {{ movie.rating }}
      </span>
      <span v-else="movie.rating == 0" class="absolute text-white text-sm">
        -
      </span>
    </div>

    <img
      :src="movie.image"
      :alt="`Poster of ${ movie.name }`"
      class="w-full h-96 object-fill"
    />
    <div class="flex flex-col items-start justify-center p-4">
      <p class="font-bold text-xl mb-2">{{ movie.name }}</p>
      <div class="mb-4">
        <span
          v-for="genre in movie.genres"
          :key="genre"
          class="inline-block bg-[#6162f0] text-white px-3 py-1 rounded-full mx-1 text-sm"
        >
          {{ genre }}
        </span>
      </div>
      <p class="mb-4 break-words">{{ movie.description }}</p>
      <div class="flex items-center gap-4 justify-between">
        <div class="flex items-center gap-1 justify-between">
          <p class="mt-1 font-bold">Your Rating: ({{ selectedRating }})</p>
          <span
            v-for="star in totalStars"
            :key="star"
            @click="setRating(star)"
            class="cursor-pointer text-2xl font-extrabold"
            :class="
              star <= selectedRating ? 'text-yellow-400' : 'text-gray-400'
            "
            >★</span
          >
        </div>
        <div
          class="absolute bottom-2 right-2 flex items-center justify-between gap-2 opacity-0 group-hover:opacity-100 transition-opacity"
        >
          <button
            @click="$emit('edit-m ovie', movie)"
            class="bg-indigo-600 hover:bg-indigo-800 text-white px-3 py-2 rounded-full"
          >
            <font-awesome-icon icon="pencil" />
          </button>
          <button
            @click="$emit('delete-movie', movie.id)"
            class="bg-gray-500 hover:bg-gray-600 text-white px-3 py-2 rounded-full"
          >
            <font-awesome-icon icon="trash" />
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, defineProps, defineEmits } from "vue";
const props = defineProps({
  movie: Object,
  totalStars: {
    type: Number,
    default: 5,
  },
});
const emit = defineEmits(["rated"]);
const selectedRating = ref(props.movie.rating);
const setRating = (star) => {
  selectedRating.value = star;
  emit("rated", props.movie.id, star);
};
watch(
  () => props.movie.rating,
  (newRating) => {
    selectedRating.value = newRating;
  }
);

</script>
