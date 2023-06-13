<template>
  <tr>
    <td>{{ product.id }}</td>
    <td>{{ product.title }}</td>
    <td>{{ product.description }}</td>
    <td>{{ product.brand }}</td>
    <td>{{ product.category }}</td>
    <td>{{ product.price }}</td>
  </tr>
</template>
  
  <script>
export default {
  props: {
    product: {
      type: Object,
      required: true,
    },
  },
};
</script>
  
  <style scoped>
/* Add custom styling if needed */
</style>
  