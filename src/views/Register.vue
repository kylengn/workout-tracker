<template>
  <div
    class="max-w-screen-sm mx-auto px-6 landscape:pt-[8rem] landscape:pb-[3rem] uppercase font-bold tracking-wider min-h-screen flex flex-col justify-center"
  >
    <!-- Error Handling -->
    <div v-if="errorMsg" class="mb-10 p-4 rounded-md bg-red-500 shadow-lg">
      <p class="text-white">{{ errorMsg }}</p>
    </div>

    <!-- Registration Form -->
    <form
      @submit.prevent="register"
      class="p-10 flex flex-col bg-black rounded-lg shadow-lg"
    >
      <h1 class="text-3xl text-white mb-4 tracking-widest">
        <span class="bg-red-500 p-1 italic">Sign</span> Up
      </h1>

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

      <!-- Register Btn -->
      <Button
        type="submit"
        class="hover:border-red-500 hover:text-red-500 bg-red-500"
        >Register</Button
      >

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
import Button from "../components/Button.vue";

export default {
  components: {
    Button,
  },
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
