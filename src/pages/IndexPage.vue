<template>
  <q-page class="row items-center justify-evenly">
    <!-- <example-component
      title="Example component"
      active
      :todos="todos"
      :meta="meta"
    ></example-component> -->

    <playground active></playground>
  </q-page>
</template>

<script setup lang="ts">
// import { ref } from 'vue';
// import ExampleComponent from 'components/ExampleComponent.vue';

// import { Todo, Meta } from 'components/threejstest/models';
import Playground from 'components/threejstest/ThreejsPlayground.vue';

defineOptions({
  name: 'IndexPage',
});
</script>
