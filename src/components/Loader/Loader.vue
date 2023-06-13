<template>
  <div class="loading-component" v-if="loading">
    <div class="spinner-border" role="status">
      <span class="visually-hidden">Loading...</span>
    </div>
  </div>
  <div v-else>
    <!-- Your main content -->
    <slot></slot>
  </div>
</template>
  
<script>
export default {
  props: {
    loading: {
      type: Boolean,
      required: true,
    },
  },
};
</script>
  
  <style scoped>
.loading-component {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50vh;
}
</style>
  