<template>
  <div class="col text-center input">
    <q-input color="black" label="Search">
        <!-- v-model="search" -->
      <template v-slot:before>
        <q-icon name="locaton_on" />
      </template>
      <template v-slot:append>
        <q-btn round dense flat icon="search" />
      </template>
    </q-input>
  </div>
</template>

<script>
export default {};
</script>

<style></style>
