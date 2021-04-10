<template>
  <div
    class="main fullscreen bg-blue text-white text-center q-pa-md flex flex-center"
  >
    <div>
      <div style="font-size: 30vh">
        404
      </div>

      <div class="text-h2" style="opacity:.4">
        Oops. Nothing here...
      </div>

      <q-btn
        class="q-mt-xl"
        color="white"
        text-color="blue"
        unelevated
        to="/"
        label="Go Back to Weather App"
        no-caps
        icon="thermostat"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "Error404"
};
</script>

<style lang="css" scoped>
.main {
  background: #2c3e50 !important;
  background: -webkit-linear-gradient(to bottom, #3498db, #2c3e50) !important;
  background: linear-gradient(to bottom, #3498db, #2c3e50) !important;
}
/* background: #2c3e50
  background: -webkit-linear-gradient(to bottom, #3498db, #2c3e50)
  background: linear-gradient(to bottom, #3498db, #2c3e50) */
</style>
