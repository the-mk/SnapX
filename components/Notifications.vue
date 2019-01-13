<template>
  <div class="ui blue tiny message" v-if="message">
    <div class="header">
      {{ message }}
    </div>
  </div>
</template>

<script>
export default {
  props: [
    'message'
  ]
}
</script>