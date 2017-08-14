<template>
  <div id="app">
    <CRSForm/>
  </div>
</template>

<script>
import CRSForm from './components/CRSForm.vue'

export default {
  name: 'app',
  components: {
    CRSForm
  }
}
</script>

<style>

@import './assets/css/normalize.css';
@import './assets/css/element.css';
@import './assets/css/app.css';

</style>
