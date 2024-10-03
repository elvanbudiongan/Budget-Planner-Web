<template>
  <div id="app">
    <BudgetPlanner />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import BudgetPlanner from "./components/BudgetPlanner.vue";

export default defineComponent({
  name: "App",
  components: {
    BudgetPlanner,
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: green;
  margin-top: 60px;
}
</style>
