<script setup>
</script>

<template>
  <div class="header">
    <a href="https://github.com/butch888/calendar-vue3">Github</a>
    <div>Calendar for "Рабочие Руки"</div>
    <div>Contact me: @vldks</div>
  </div>
</template>

<style scoped>

  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 30px;
    background-color: #f0f0f0;
    border-bottom: 1px solid #ccc;
    font-weight: bold;
  }

  .header a {
    text-decoration: none;
    color: #333;
    border-bottom: 3px solid rgba(0, 0, 0, 0);

  }

  .header a:hover {
    border-bottom: 3px solid rgba(0, 0, 0, 1);
    transition: ease-in 0.5s;
  }

</style>