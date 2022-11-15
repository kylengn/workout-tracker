<template>
  <div
    class="max-w-screen-sm mx-auto px-4 py-10 text-white uppercase font-bold tracking-wider"
  >
    <!-- App Message -->
    <div
      v-if="statusMsg || errorMsg"
      class="mb-10 p-4 rounded-md shadow-md bg-light-grey"
    >
      <p class="text-at-light-green">{{ statusMsg }}</p>
      <p class="text-red-500">{{ errorMsg }}</p>
    </div>

    <!-- Data -->
    <div v-if="dataLoaded">
      <!-- General Workout Info -->
      <div
        class="flex flex-col items-center p-10 rounded-md shadow-md bg-black relative"
      >
        <div v-if="user" class="flex absolute left-4 top-2 gap-2">
          <div
            class="h-7 w-7 rounded-full flex justify-center items-center cursor-pointer bg-light-grey shadow-lg"
            @click="editMode"
          >
            <img
              class="h-4 w-auto"
              src="../assets/images/pencil-simple-fill.png"
              alt=""
            />
          </div>
          <div
            class="h-7 w-7 rounded-full flex justify-center items-center cursor-pointer bg-light-grey shadow-lg"
            @click="deleteWorkout"
          >
            <img
              class="h-4 w-auto"
              src="../assets/images/trash-simple-fill.png"
              alt=""
            />
          </div>
        </div>

        <!-- Cardio Image -->
        <img
          v-if="data.workoutType === 'cardio'"
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
          {{ data.workoutType }}
        </p>

        <div class="w-full mt-6">
          <input
            v-if="edit"
            type="text"
            class="p-2 w-full text-gray-500 focus:outline-none"
            v-model="data.workoutName"
          />
          <h1 v-else class="text-2xl text-center">{{ data.workoutName }}</h1>
        </div>
      </div>

      <!-- Exercises -->
      <div
        class="mt-10 p-10 rounded-md flex flex-col items-center bg-black shadow-md"
      >
        <!-- Strength Training -->
        <div
          v-if="data.workoutType === 'strength'"
          class="flex flex-col gap-y-4 w-full"
        >
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative sm:flex-row"
            v-for="(item, index) in data.exercises"
            :key="index"
          >
            <div class="flex flex-2 flex-col md:w-1/3">
              <label
                for="exercise-name"
                class="mb-1 text-md text-red-500 italic"
                >Exercise</label
              >
              <input
                v-if="edit"
                type="text"
                id="exercise-name"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.exercise"
              />
              <p v-else class="capitalize">{{ item.exercise }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="sets" class="mb-1 text-md text-red-500 italic"
                >Sets</label
              >
              <input
                v-if="edit"
                type="text"
                id="sets"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.sets"
              />
              <p v-else class="capitalize">{{ item.sets }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="reps" class="mb-1 text-md text-red-500 italic"
                >Reps</label
              >
              <input
                v-if="edit"
                type="text"
                id="reps"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.reps"
              />
              <p v-else class="capitalize">{{ item.reps }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="weight" class="mb-1 text-md text-red-500 italic"
                >Weight</label
              >
              <input
                v-if="edit"
                type="text"
                id="weight"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.weight"
              />
              <p v-else class="capitalize">{{ item.weight }}</p>
            </div>

            <img
              v-if="edit"
              @click="deleteExercise(item.id)"
              src="../assets/images/trash-simple-fill.png"
              class="h-4 w-auto absolute -left-6 top-1 cursor-pointer"
              alt=""
            />
          </div>

          <button
            v-if="edit"
            @click="addExercise"
            type="button"
            class="mt-6 py-2 px-6 rounded-full self-start uppercase tracking-wider text-white bg-red-500 duration-200 border-solid border-2 border-transparent hover:border-red-500 hover:bg-transparent hover:text-red-500"
          >
            Add Exercise
          </button>
        </div>

        <!-- Cardio Training -->
        <div v-else class="flex flex-col gap-y-4 w-full">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative sm:flex-row"
            v-for="(item, index) in data.exercises"
            :key="index"
          >
            <div class="flex flex-2 flex-col md:w-1/3">
              <label for="cardio-type" class="mb-1 text-md text-red-500 italic"
                >Cardio Type</label
              >
              <select
                v-if="edit"
                id="cardio-type"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.cardioType"
              >
                <option value="#">Select Type</option>
                <option value="run">Run</option>
                <option value="spin">Spin</option>
              </select>
              <p v-else class="capitalize">{{ item.cardioType }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="distance" class="mb-1 text-md text-red-500 italic"
                >Distance</label
              >
              <input
                v-if="edit"
                type="text"
                id="distance"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.distance"
              />
              <p v-else class="capitalize">{{ item.distance }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="duration" class="mb-1 text-md text-red-500 italic"
                >Duration</label
              >
              <input
                v-if="edit"
                type="text"
                id="duration"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.duration"
              />
              <p v-else class="capitalize">{{ item.duration }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="pace" class="mb-1 text-md text-red-500 italic"
                >Pace</label
              >
              <input
                v-if="edit"
                type="text"
                id="pace"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.pace"
              />
              <p v-else class="capitalize">{{ item.pace }}</p>
            </div>

            <img
              v-if="edit"
              @click="deleteExercise(item.id)"
              src="../assets/images/trash-simple-fill.png"
              class="h-4 w-auto absolute -left-6 top-1 cursor-pointer"
              alt=""
            />
          </div>

          <button
            v-if="edit"
            @click="addExercise"
            type="button"
            class="mt-6 py-2 px-6 rounded-full self-start uppercase tracking-wider text-white bg-red-500 duration-200 border-solid border-2 border-transparent hover:border-red-500 hover:bg-transparent hover:text-red-500"
          >
            Add Exercise
          </button>
        </div>
      </div>

      <!-- Update -->
      <button
        v-if="edit"
        @click="update"
        type="button"
        class="mt-6 py-2 px-6 rounded-full self-start uppercase tracking-wider text-white bg-red-500 duration-200 border-solid border-2 border-transparent hover:border-red-500 hover:bg-transparent hover:text-red-500"
      >
        Update Workout
      </button>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabase/init";
import { useRoute, useRouter } from "vue-router";
import { uid } from "uid";
import store from "../store/index";
import { computed } from "vue";

export default {
  name: "view-workout",
  setup() {
    // Create data / vars
    const data = ref(null);
    const dataLoaded = ref(null);
    const errorMsg = ref(null);
    const statusMsg = ref(null);
    const route = useRoute();
    const router = useRouter();
    const user = computed(() => store.state.user);

    // Get current Id of route
    const currentId = route.params.workoutId;

    // Get workout data
    const getData = async () => {
      try {
        const { data: workouts, error } = await supabase
          .from("workouts")
          .select("*")
          .eq("id", currentId);
        if (error) throw error;
        data.value = workouts[0];
        dataLoaded.value = true;
        console.log(data.value);
      } catch (error) {
        errorMsg.value = error.message;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    getData();

    // Delete workout
    const deleteWorkout = async () => {
      try {
        const { error } = await supabase
          .from("workouts")
          .delete()
          .eq("id", currentId);

        if (error) throw error;
        router.push({ name: "Home" });
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };

    // Edit mode
    const edit = ref(null);

    const editMode = () => {
      edit.value = !edit.value;
    };

    // Add Exercise
    const addExercise = () => {
      if (data.value.workoutType === "strength") {
        data.value.exercises.push({
          id: uid(),
          exercise: "",
          sets: "",
          reps: "",
          weight: "",
        });
        return;
      }

      data.value.exercises.push({
        id: uid(),
        cardioType: "",
        distance: "",
        duration: "",
        pace: "",
      });
    };

    // Delete exercise
    const deleteExercise = (id) => {
      if (data.value.exercises.length > 1) {
        data.value.exercises = data.value.exercises.filter(
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

    // Update workout
    const update = async () => {
      try {
        const { error } = await supabase
          .from("workouts")
          .update({
            workoutName: data.value.workoutName,
            exercises: data.value.exercises,
          })
          .eq("id", currentId);
        if (error) throw error;
        edit.value = false;
        statusMsg.value = "Success: Workout Updated!";
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
      data,
      dataLoaded,
      errorMsg,
      statusMsg,
      edit,
      editMode,
      user,
      deleteWorkout,
      addExercise,
      deleteExercise,
      update,
    };
  },
};
</script>