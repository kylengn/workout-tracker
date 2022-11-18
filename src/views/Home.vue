<template>
  <div
    v-if="dataLoaded"
    class="container px-8 md:px-6 pt-[10rem] landscape:pt-[8rem] landscape:pb-[3rem] md:pt-0 pb-8 md:pb-0 uppercase font-bold tracking-wider text-white min-h-screen flex flex-col justify-center"
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
        class="flex flex-col items-center bg-black p-10 shadow-lg cursor-pointer rounded-lg hover:scale-105 transition-transform"
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

  <!-- Spinner While Loading Data -->
  <div
    v-if="!dataLoaded"
    class="container px-8 md:px-6 pt-[10rem] landscape:pt-[8rem] landscape:pb-[3rem] md:pt-0 pb-8 md:pb-0 uppercase font-bold tracking-wider text-white min-h-screen flex flex-col justify-center"
  >
    <div class="w-full flex flex-col items-center">
      <svg
        class="h-[10rem] w-auto animate-spin"
        xmlns="http://www.w3.org/2000/svg"
        width="192"
        height="192"
        fill="#ef4406"
        viewBox="0 0 256 256"
      >
        <rect width="256" height="256" fill="none"></rect>
        <path
          d="M124,136a8,8,0,0,1-8-8,16,16,0,0,1,16-16,25.9,25.9,0,0,1,26,26,36,36,0,0,1-36,36,46,46,0,0,1-46-46,56,56,0,0,1,56-56,65.9,65.9,0,0,1,66,66,76,76,0,0,1-76,76,86,86,0,0,1-86-86,96,96,0,0,1,96-96A106.1,106.1,0,0,1,238,138"
          fill="none"
          stroke="#ef4406"
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="24"
        ></path>
      </svg>
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
    const dataLoaded = ref(false);

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
