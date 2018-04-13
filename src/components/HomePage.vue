<template>
  <div>
    <div v-if="loggedIn">
      <GroceryCart/>
    </div>
    <div v-else>
      <welcome-page/>
    </div>
  </div>
</template>

<script>
 import WelcomePage from './WelcomePage';
 import GroceryCart from './GroceryCart';
 export default {
   name: 'HomePage',
   components: {WelcomePage, GroceryCart},
   computed: {
     loggedIn: function() {
       return this.$store.getters.loggedIn;
     },
   },
 }
</script>

<style scoped>
</style>