<template>
  <div class="home">
    <img src="/img/logo.png" width="100"><br><br>
    <HelloWorld msg="Welcome to Your Vue.js App"/>
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