<template>
  <header
    class="bg-black text-white uppercase font-bold tracking-widest fixed w-full z-10"
  >
    <nav
      class="container py-5 px-4 flex flex-col gap-4 items-center sm:flex-row"
    >
      <div class="flex items-center gap-x-4">
        <img class="w-8" src="../assets/images/bum.png" alt="" />
        <h1 class="text-lg">
          Gainz <span class="bg-red-500 p-1 italic">Tracker</span>
        </h1>
      </div>
      <ul class="flex flex-1 justify-end gap-x-10">
        <router-link
          v-if="user"
          class="cursor-pointer hover:text-red-500 duration-300"
          :to="{ name: 'Home' }"
          >Home</router-link
        >
        <router-link
          v-if="user"
          class="cursor-pointer hover:text-red-500 duration-300"
          :to="{ name: 'Create' }"
          >Create</router-link
        >
        <!-- <router-link
          v-if="!user"
          class="cursor-pointer hover:text-red-500 duration-300"
          :to="{ name: 'Login' }"
          >Login</router-link
        > -->
        <li
          v-if="user"
          @click="logout"
          class="cursor-pointer hover:text-red-500 duration-300"
        >
          Logout
        </li>
      </ul>
    </nav>
  </header>
</template>

<script>
import { supabase } from "../supabase/init";
import { useRouter } from "vue-router";
import store from "../store/index";
import { computed } from "vue";

export default {
  setup() {
    // Get user from store
    const user = computed(() => store.state.user);
    // Set up ref to router
    const router = useRouter();

    // Logout function
    const logout = async () => {
      await supabase.auth.signOut();
      router.push({ name: "Login" });
    };

    return {
      logout,
      user,
    };
  },
};
</script>

<style>
.active {
  color: rgb(239 68 68 / 1);
}
</style>
