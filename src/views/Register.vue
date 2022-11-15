<template>
  <div
    class="max-w-screen-sm mx-auto px-4 py-10 uppercase font-bold tracking-wider"
  >
    <!-- Error Handling -->
    <div v-if="errorMsg" class="mb-10 p-4 rounded-md bg-red-500 shadow-lg">
      <p class="text-white">{{ errorMsg }}</p>
    </div>

    <!-- Registration Form -->
    <form
      @submit.prevent="register"
      class="p-10 flex flex-col bg-black rounded-md shadow-lg"
    >
      <h1 class="text-3xl text-white mb-4">Register</h1>

      <div class="flex flex-col mb-2">
        <label for="email" class="mb-1 text-sm text-white">Email</label>
        <input
          type="text"
          id="email"
          class="p-2 text-gray-500 focus:outline-none"
          required
          v-model="email"
        />
      </div>

      <div class="flex flex-col mb-2">
        <label for="password" class="mb-1 text-sm text-white">Password</label>
        <input
          type="password"
          id="password"
          class="p-2 text-gray-500 focus:outline-none"
          required
          v-model="password"
        />
      </div>

      <div class="flex flex-col mb-2">
        <label for="confirmPassword" class="mb-1 text-sm text-white"
          >Confirm Password</label
        >
        <input
          type="password"
          id="confirmPassword"
          class="p-2 text-gray-500 focus:outline-none"
          required
          v-model="confirmPassword"
        />
      </div>

      <button
        type="submit"
        class="mt-6 py-2 px-6 rounded-full self-start uppercase tracking-wider text-white bg-red-500 duration-200 border-solid border-2 border-transparent hover:border-red-500 hover:bg-transparent hover:text-white"
      >
        Register
      </button>

      <div class="text-sm mt-6 text-center text-white">
        Already have an account?
        <router-link :to="{ name: 'Login' }" class="text-red-500"
          >Login</router-link
        >
      </div>
    </form>
  </div>
</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabase/init";
import { useRouter } from "vue-router";

export default {
  name: "register",
  setup() {
    // Create data / vars
    const router = useRouter();
    const email = ref(null);
    const password = ref(null);
    const confirmPassword = ref(null);
    const errorMsg = ref(null);

    // Register function
    const register = async () => {
      if (password.value === confirmPassword.value) {
        try {
          let { error } = await supabase.auth.signUp({
            email: email.value,
            password: password.value,
          });

          if (error) throw error;
          router.push({ name: "Login" });
        } catch (error) {
          errorMsg.value = error.message;
          setTimeout(() => {
            errorMsg.value = null;
          }, 5000);
        }
        return;
      }

      errorMsg.value = "Error: Passwords do not match!";
      setTimeout(() => {
        errorMsg.value = null;
      }, 5000);
    };

    return {
      email,
      password,
      confirmPassword,
      errorMsg,
      register,
    };
  },
};
</script>
