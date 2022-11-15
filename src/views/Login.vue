<template>
  <div
    class="max-w-screen-sm mx-auto px-4 py-10 uppercase font-bold tracking-wider"
  >
    <!-- Error Handling -->
    <div v-if="errorMsg" class="mb-10 p-4 rounded-md bg-red-500 shadow-lg">
      <p class="text-white">{{ errorMsg }}</p>
    </div>

    <!-- Login Form -->
    <form
      @submit.prevent="login"
      class="p-10 flex flex-col bg-black rounded-md shadow-lg"
    >
      <h1 class="text-3xl text-white mb-4">Login</h1>

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

      <button
        type="submit"
        class="mt-6 py-2 px-6 rounded-full self-start uppercase tracking-wider text-white bg-red-500 duration-200 border-solid border-2 border-transparent hover:border-red-500 hover:bg-transparent hover:text-white"
      >
        Login
      </button>

      <div class="text-sm mt-6 text-center text-white">
        Don't have an account yet?
        <router-link :to="{ name: 'Register' }" class="text-red-500"
          >Sign Up</router-link
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
  name: "login",
  setup() {
    // Create data / vars
    const router = useRouter();
    const email = ref(null);
    const password = ref(null);
    const errorMsg = ref(null);

    // Login function
    const login = async () => {
      try {
        let { error } = await supabase.auth.signInWithPassword({
          email: email.value,
          password: password.value,
        });

        if (error) throw error;
        router.push({ name: "Home" });
      } catch (error) {
        errorMsg.value = `Error: ${error.message}!`;
        setTimeout(() => {
          errorMsg.value = null;
        }, 5000);
      }
      return;
    };

    return {
      email,
      password,
      errorMsg,
      login,
    };
  },
};
</script>
