<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <div class="juego">
      <BuscaMinas/>
    </div>
    
  </div>
</template>

<script>
// @ is an alias to /src
import BuscaMinas from '@/components/BuscaMinas.vue'

export default {
  name: 'Home',
  components: {
    BuscaMinas
  }
}
</script>

<style scoped>
.juego {
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}

</style>