<template>
  <div class="container" id="app">
      <h2>Axios & Vuelidate</h2>
      <vueForm/>
  </div>
</template>

<script>
import vueForm from './components/vueForm'
export default {
  components: {
    vueForm
  }
}
</script>

<style lang='scss'>
  @import '@/assets/scss/app.scss';
</style>