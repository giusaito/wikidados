<template>
  <q-page class="flex flex-center home-page">
    <img
      alt="Wiki Dados"
      src="~assets/logo_verde_fundo_escuro.png"
      style="width: 200px; height: 200px"
    >
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'PageIndex'
})
</script>
<style lang="scss">
  .home-page {
    background:url(~assets/fundo_1.jpg) no-repeat;
    background-size:cover;
  }
</style>