<template>
  <div
    v-if="dataLoaded"
    class="container mt-10 px-4 uppercase font-bold tracking-wider text-white"
  >
    <!-- No Data -->
    <div v-if="data.length === 0" class="w-full flex flex-col items-center">
      <h1 class="text-2xl text-black">No Exercises Yet!</h1>
      <router-link
        :to="{ name: 'Create' }"
        class="mt-6 py-2 px-6 rounded-full uppercase tracking-wider text-white bg-red-500 duration-200 border-solid border-2 border-transparent hover:border-red-500 hover:bg-transparent hover:text-red-500 shadow-md"
        >Create Workout</router-link
      >
    </div>

    <!-- Data -->
    <div
      v-else
      class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6"
    >
      <router-link
        class="flex flex-col items-center bg-black p-10 shadow-md cursor-pointer"
        :to="{ name: 'View-Workout', params: { workoutId: workout.id } }"
        v-for="(workout, index) in data"
        :key="index"
      >
        <!-- Cardio Image -->
        <img
          v-if="workout.workoutType === 'cardio'"
          src="../assets/images/dumbbell.png"
          class="h-24 w-auto"
          alt=""
        />

        <!-- Strength Image -->
        <img
          v-else
          src="../assets/images/muscles.png"
          class="h-24 w-auto"
          alt=""
        />

        <p class="mt-6 py-1 px-3 text-xs bg-red-500 shadow-md rounded-full">
          {{ workout.workoutType }}
        </p>

        <h1 class="mt-8 mb-2 text-center text-xl">{{ workout.workoutName }}</h1>
      </router-link>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabase/init";

export default {
  name: "home",
  components: {},
  setup() {
    // Create data / vars
    const data = ref([]);
    const dataLoaded = ref(null);

    // Get data
    const getData = async () => {
      try {
        const { data: workouts, error } = await supabase
          .from("workouts")
          .select("*");
        if (error) throw error;
        data.value = workouts;
        dataLoaded.value = true;
      } catch (error) {
        console.warn(error.message);
      }
    };

    // Run data function
    getData();

    return { data, dataLoaded };
  },
};
</script>