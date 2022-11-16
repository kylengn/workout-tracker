<template>
  <div
    class="max-w-screen-md px-8 md:px-6 mx-auto pt-[10rem] md:pt-0 pb-8 md:pb-0 uppercase font-bold tracking-wider min-h-screen flex flex-col justify-center"
  >
    <!-- Status Message -->
    <div v-if="errorMsg" class="mb-10 p-4 rounded-md bg-red-500 shadow-lg">
      <p class="text-white">{{ errorMsg }}</p>
    </div>

    <div
      v-if="statusMsg"
      class="mb-10 p-4 rounded-md bg-at-light-green shadow-lg"
    >
      <p class="text-white">{{ statusMsg }}</p>
    </div>

    <!-- Create -->
    <div class="p-10 flex items-start bg-black text-white rounded-lg shadow-lg">
      <!-- Form -->
      <form
        @submit.prevent="createWorkout"
        class="flex flex-col gap-y-5 w-full"
      >
        <h1 class="text-2xl tracking-widest">
          <span class="bg-red-500 p-1 italic">Record</span>
          Workout
        </h1>

        <!-- Workout Name -->
        <div class="flex flex-col">
          <label for="workout-name" class="mb-1 text-sm">Workout Name</label>
          <input
            type="text"
            id="workout-name"
            required
            class="p-2 text-gray-500 focus:outline-none"
            v-model="workoutName"
          />
        </div>

        <!-- Workout Type -->
        <div class="flex flex-col">
          <label for="workout-type" class="mb-1 text-sm">Workout Type</label>
          <select
            id="workout-type"
            required
            class="p-2 text-gray-500 focus:outline-none"
            v-model="workoutType"
            @change="workoutChange"
          >
            <option value="#">Select Type</option>
            <option value="strength">Strength</option>
            <option value="cardio">Cardio</option>
          </select>
        </div>

        <!-- Strength Training Inputs -->
        <div v-if="workoutType === 'strength'" class="flex flex-col gap-y-4">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative md:flex-row"
            v-for="(item, index) in exercises"
            :key="index"
          >
            <div class="flex flex-col md:w-1/3">
              <label for="exercise-name" class="mb-1 text-sm">Exercise</label>
              <input
                type="text"
                id="exercise-name"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.exercise"
              />
            </div>
            <div class="flex flex-col flex-1">
              <label for="sets" class="mb-1 text-sm">Sets</label>
              <input
                type="text"
                id="sets"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.sets"
              />
            </div>
            <div class="flex flex-col flex-1">
              <label for="reps" class="mb-1 text-sm">Reps</label>
              <input
                type="text"
                id="reps"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.reps"
              />
            </div>
            <div class="flex flex-col flex-1">
              <label for="weight" class="mb-1 text-sm">Weight (Kg's)</label>
              <input
                type="text"
                id="weight"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.weight"
              />
            </div>

            <img
              @click="deleteExercise(item.id)"
              src="../assets/images/trash-simple-fill.png"
              class="h-6 w-auto absolute -left-8 cursor-pointer"
              alt=""
            />
          </div>
          <button
            @click="addExercise"
            type="button"
            class="mt-6 py-2 px-6 rounded-full self-start uppercase tracking-wider text-white bg-red-500 duration-200 border-solid border-2 border-transparent hover:border-red-500 hover:bg-transparent hover:text-red-500"
          >
            Add Exercise
          </button>
        </div>

        <!-- Cardio Training Inputs -->
        <div v-if="workoutType === 'cardio'" class="flex flex-col gap-y-4">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative md:flex-row"
            v-for="(item, index) in exercises"
            :key="index"
          >
            <div class="flex flex-col md:w-1/3">
              <label for="cardio-type" class="mb-1 text-sm">Cardio Type</label>
              <select
                id="cardio-type"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.cardioType"
              >
                <option value="#">Select Type</option>
                <option value="run">Run</option>
                <option value="spin">Spin</option>
              </select>
            </div>
            <div class="flex flex-col flex-1">
              <label for="distance" class="mb-1 text-sm">Distance</label>
              <input
                type="text"
                id="distance"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.distance"
              />
            </div>
            <div class="flex flex-col flex-1">
              <label for="duration" class="mb-1 text-sm">Duration</label>
              <input
                type="text"
                id="duration"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.duration"
              />
            </div>
            <div class="flex flex-col flex-1">
              <label for="pace" class="mb-1 text-sm">Pace</label>
              <input
                type="text"
                id="pace"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.pace"
              />
            </div>

            <img
              @click="deleteExercise(item.id)"
              src="../assets/images/trash-simple-fill.png"
              class="h-6 w-auto absolute -left-8 cursor-pointer"
              alt=""
            />
          </div>
          <!-- Add Exercise Btn -->
          <Button @click="addExercise">Add Exercise</Button>
        </div>

        <!-- Create Workout Btn -->
        <Button type="submit">Create Workout</Button>
      </form>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { uid } from "uid";
import { supabase } from "../supabase/init";
import Button from "../components/Button.vue";

export default {
  components: {
    Button,
  },
  name: "create",
  setup() {
    // Create data
    const workoutName = ref("");
    const workoutType = ref("select-workout");
    const exercises = ref([]);
    const statusMsg = ref(null);
    const errorMsg = ref(null);

    // Add exercise
    const addExercise = () => {
      if (workoutType.value === "strength") {
        exercises.value.push({
          id: uid(),
          exercise: "",
          sets: "",
          reps: "",
          weight: "",
        });
        return;
      }

      exercises.value.push({
        id: uid(),
        cardioType: "",
        distance: "",
        duration: "",
        pace: "",
      });
    };

    // Delete exercise
    const deleteExercise = (id) => {
      if (exercises.value.length > 1) {
        exercises.value = exercises.value.filter(
          (exercise) => exercise.id !== id
        );
        return;
      }

      errorMsg.value =
        "Error: Cannot remove, need to at least have one exercise!";
      setTimeout(() => {
        errorMsg.value = false;
      }, 5000);
    };

    // Listens for changing of workout type input
    const workoutChange = () => {
      exercises.value = [];
      addExercise();
    };

    // Create workout
    const createWorkout = async () => {
      try {
        const { error } = await supabase.from("workouts").insert([
          {
            workoutName: workoutName.value,
            workoutType: workoutType.value,
            exercises: exercises.value,
          },
        ]);
        if (error) throw error;
        statusMsg.value = "Success: Workout Recorded!";
        workoutName.value = null;
        workoutType.value = "select-workout";
        exercises.value = [];
        setTimeout(() => {
          statusMsg.value = false;
        }, 5000);
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    return {
      workoutName,
      workoutType,
      exercises,
      statusMsg,
      errorMsg,
      addExercise,
      workoutChange,
      deleteExercise,
      createWorkout,
    };
  },
};
</script>
