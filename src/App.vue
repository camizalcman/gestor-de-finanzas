<script setup>
  const number = 5

  function sum(a,b){
    return a+b;
  }
</script>

<template>
  {{ sum(5,6) }}
</template>

<style scoped>

  .text-red{
    color:red
  }
</style>
