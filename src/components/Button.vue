<template>
  <button
    :type="type"
    class="mt-6 py-2 px-6 rounded-full self-start uppercase tracking-wider text-white duration-200 border-solid border-2 border-transparent hover:bg-transparent"
  >
    <slot />
  </button>
</template>

<script>
export default {
  name: "Button",
  props: {
    type: {
      type: String,
      default: "button",
    },
  },
};
</script>
