<template>
  <div
    class="max-w-screen-sm text-white px-8 md:px-6 mx-auto pt-[10rem] landscape:pt-[8rem] landscape:pb-[3rem] pb-8 uppercase font-bold tracking-wider min-h-screen flex flex-col justify-center"
  >
    <!-- App Message -->
    <div v-if="errorMsg" class="mb-10 p-4 rounded-md bg-red-500 shadow-lg">
      <p class="text-white">{{ errorMsg }}</p>
    </div>

    <div
      v-if="statusMsg"
      class="mb-10 p-4 rounded-md bg-at-light-green shadow-lg"
    >
      <p class="text-white">{{ statusMsg }}</p>
    </div>

    <!-- Data -->
    <div v-if="dataLoaded">
      <!-- General Workout Info -->
      <div
        class="flex flex-col items-center p-10 rounded-lg shadow-lg bg-black relative"
      >
        <div v-if="user" class="flex absolute left-6 top-4 gap-2">
          <div
            class="h-10 w-10 rounded-full flex justify-center items-center cursor-pointer bg-light-grey shadow-lg"
            @click="editMode"
          >
            <img
              class="h-6 w-auto"
              src="../assets/images/pencil-simple-fill.png"
              alt=""
            />
          </div>
          <div
            class="h-10 w-10 rounded-full flex justify-center items-center cursor-pointer bg-light-grey shadow-lg"
            @click="showDeletionModal"
          >
            <img
              class="h-6 w-auto"
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
          v-else-if="data.workoutType === 'strength'"
          src="../assets/images/muscles.png"
          class="h-24 w-auto"
          alt=""
        />

        <!-- Rest Image -->
        <img
          v-else
          src="../assets/images/recovery.png"
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
        class="mt-10 p-10 rounded-lg shadow-lg flex flex-col items-center bg-black"
      >
        <!-- Strength Training -->
        <div
          v-if="data.workoutType === 'strength'"
          class="flex flex-col gap-y-10 w-full"
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
          <!-- Exercise Counter -->
          <div
            class="flex space-x-2 border-t-2 border-light-grey border-double pt-6 text-lg"
          >
            <p class="text-red-500">Total Exercise(s):</p>
            <span>{{ data.exercises.length }}</span>
          </div>

          <!-- Add Exercise Btn -->
          <Button
            v-if="edit"
            @click="addExercise"
            class="hover:border-red-500 hover:text-red-500 bg-red-500"
            >Add Exercise</Button
          >
        </div>

        <!-- Cardio Training -->
        <div
          v-if="data.workoutType === 'cardio'"
          class="flex flex-col gap-y-10 w-full"
        >
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
                <optgroup label="Cardio Type">
                  <option value="Boxing">Boxing</option>
                  <option value="Battle Rope">Battle Rope</option>
                  <option value="Skipping">Skipping</option>
                </optgroup>
              </select>
              <p v-else class="capitalize">{{ item.cardioType }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="distance" class="mb-1 text-md text-red-500 italic"
                >Rounds</label
              >
              <input
                v-if="edit"
                type="text"
                id="rounds"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.rounds"
              />
              <p v-else class="capitalize">{{ item.rounds }}</p>
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

            <img
              v-if="edit"
              @click="deleteExercise(item.id)"
              src="../assets/images/trash-simple-fill.png"
              class="h-4 w-auto absolute -left-6 top-1 cursor-pointer"
              alt=""
            />
          </div>

          <!-- Exercise Counter -->
          <div
            class="flex space-x-2 border-t-2 border-light-grey border-double pt-6 text-lg"
          >
            <p class="text-red-500">Total Exercise(s):</p>
            <span>{{ data.exercises.length }}</span>
          </div>

          <!-- Add Exercise Btn -->
          <Button
            v-if="edit"
            @click="addExercise"
            class="hover:border-red-500 hover:text-red-500 bg-red-500"
            >Add Exercise</Button
          >
        </div>

        <!-- Rest Training -->
        <div
          v-if="data.workoutType === 'rest'"
          class="flex flex-col gap-y-10 w-full"
        >
          <div
            class="flex flex-col gap-x-6 gap-y-2 relative sm:flex-row"
            v-for="(item, index) in data.exercises"
            :key="index"
          >
            <div class="flex flex-2 flex-col md:w-1/3">
              <label for="rest-type" class="mb-1 text-md text-red-500 italic"
                >Rest Type</label
              >
              <select
                v-if="edit"
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
              <p v-else class="capitalize">{{ item.restType }}</p>
            </div>
            <div class="flex flex-col flex-1">
              <label for="distance" class="mb-1 text-md text-red-500 italic"
                >Rounds</label
              >
              <input
                v-if="edit"
                type="text"
                id="rounds"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.rounds"
              />
              <p v-else class="capitalize">{{ item.rounds }}</p>
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

            <img
              v-if="edit"
              @click="deleteExercise(item.id)"
              src="../assets/images/trash-simple-fill.png"
              class="h-4 w-auto absolute -left-6 top-1 cursor-pointer"
              alt=""
            />
          </div>

          <!-- Exercise Counter -->
          <div
            class="flex space-x-2 border-t-2 border-light-grey border-double pt-6 text-lg"
          >
            <p class="text-red-500">Total Exercise(s):</p>
            <span>{{ data.exercises.length }}</span>
          </div>

          <!-- Add Exercise Btn -->
          <Button
            v-if="edit"
            @click="addExercise"
            class="hover:border-red-500 hover:text-red-500 bg-red-500"
            >Add Exercise</Button
          >
        </div>
      </div>

      <!-- Update -->
      <Button
        v-if="edit"
        @click="update"
        class="hover:border-red-500 hover:text-red-500 bg-red-500"
        >Update Workout</Button
      >
    </div>

    <!-- Spinner While Loading Data -->

    <div v-if="!dataLoaded" class="w-full flex flex-col items-center">
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

    <!-- Deletion Modal Popup -->
    <div
      v-show="isModalVisible"
      class="absolute inset-0 bg-black/50 min-h-screen min-w-screen px-8"
    >
      <div
        class="flex flex-col justify-center items-center container bg-white p-10 mt-[10rem] max-w-2xl w-full rounded-lg shadow-lg text-black gap-y-6"
      >
        <h1>Are you sure you want to delete?</h1>
        <div class="flex justify-center items-center gap-x-4">
          <Button
            @click="closeDeletionModal"
            class="hover:border-at-light-green hover:text-at-light-green bg-at-light-green"
            >Cancel</Button
          >
          <Button
            @click="deleteWorkout"
            class="hover:border-red-500 hover:text-red-500 bg-red-500"
            >Delete</Button
          >
        </div>
      </div>
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
import Button from "../components/Button.vue";

export default {
  components: {
    Button,
  },
  name: "view-workout",
  data() {
    return {
      isModalVisible: false,
    };
  },
  methods: {
    showDeletionModal() {
      this.isModalVisible = true;
    },
    closeDeletionModal() {
      this.isModalVisible = false;
    },
  },
  setup() {
    // Create data / vars
    const data = ref(null);
    const dataLoaded = ref(false);
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
