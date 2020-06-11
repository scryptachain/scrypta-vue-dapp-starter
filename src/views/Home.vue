<template>
  <div class="home">
    <img src="/logo.png" width="100"><br><br>
    <HelloWorld />
  </div>
</template>

<script>
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'Home',
  components: {
    HelloWorld
  }
}
</script>
<style>
  .home{
    padding: 20px;
  }
</style> 