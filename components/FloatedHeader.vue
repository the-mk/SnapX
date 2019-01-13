<template>
  <div>
    <h2 class="ui left floated header">{{ title }}</h2>
    <div class="ui clearing divider"></div>    
  </div>
</template>
<script>

export default {
  props: [
    'title'
  ]
}
</script>