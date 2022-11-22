<template>
  <div
    class="max-w-screen-md px-8 md:px-6 mx-auto pt-[10rem] landscape:pt-[8rem] landscape:pb-[3rem] md:pt-0 pb-8 md:pb-0 uppercase font-bold tracking-wider min-h-screen flex flex-col justify-center"
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
            <optgroup label="Select Type">
              <option value="strength">Strength</option>
              <option value="cardio">Cardio</option>
              <option value="rest">Rest</option>
            </optgroup>
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
              <label for="weight" class="mb-1 text-sm">Weight</label>
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
          <!-- Add Exercise Btn -->
          <Button
            @click="addExercise"
            class="hover:border-red-500 hover:text-red-500 bg-red-500"
            >Add Exercise</Button
          >
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
                <optgroup label="Cardio Type">
                  <option value="Boxing">Boxing</option>
                  <option value="Battle Rope">Battle Rope</option>
                  <option value="Skipping">Skipping</option>
                </optgroup>
              </select>
            </div>
            <div class="flex flex-col flex-1">
              <label for="rounds" class="mb-1 text-sm">Rounds</label>
              <input
                type="text"
                id="rounds"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.rounds"
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

            <img
              @click="deleteExercise(item.id)"
              src="../assets/images/trash-simple-fill.png"
              class="h-6 w-auto absolute -left-8 cursor-pointer"
              alt=""
            />
          </div>
          <!-- Add Exercise Btn -->
          <Button
            @click="addExercise"
            class="hover:border-red-500 hover:text-red-500 bg-red-500"
            >Add Exercise</Button
          >
        </div>

        <!-- Rest Traiining Inputs -->
        <div v-if="workoutType === 'rest'" class="flex flex-col gap-y-4">
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative md:flex-row"
            v-for="(item, index) in exercises"
            :key="index"
          >
            <div class="flex flex-col md:w-1/3">
              <label for="rest-type" class="mb-1 text-sm">Rest Type</label>
              <select
                id="rest-type"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.restType"
              >
                <optgroup label="Rest Type">
                  <option value="Hot Sauna">Hot Sauna</option>
                  <option value="Cold (or Ice) Bath/Shower">
                    Cold (or Ice) Bath/Shower
                  </option>
                </optgroup>
              </select>
            </div>
            <div class="flex flex-col flex-1">
              <label for="rounds" class="mb-1 text-sm">Rounds</label>
              <input
                type="text"
                id="rounds"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.rounds"
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

            <img
              @click="deleteExercise(item.id)"
              src="../assets/images/trash-simple-fill.png"
              class="h-6 w-auto absolute -left-8 cursor-pointer"
              alt=""
            />
          </div>
          <!-- Add Exercise Btn -->
          <Button
            @click="addExercise"
            class="hover:border-red-500 hover:text-red-500 bg-red-500"
            >Add Exercise</Button
          >
        </div>

        <!-- Create Workout Btn -->
        <Button
          type="submit"
          class="hover:border-red-500 hover:text-red-500 bg-red-500"
          >Create Workout</Button
        >
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

      if (workoutType.value === "cardio") {
        exercises.value.push({
          id: uid(),
          cardioType: "",
          distance: "",
          duration: "",
          pace: "",
        });
        return;
      }

      exercises.value.push({
        id: uid(),
        restType: "",
        rounds: "",
        duration: "",
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
