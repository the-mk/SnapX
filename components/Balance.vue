<template>
  <div class="ui basic center aligned segment" > 
    <h1 v-if="balance != null" class="ui header" style="color: #48d0e0">
      {{ balance.toLocaleString('en') }} PAY
      <div class="sub header" style="text-transform: uppercase; font-size: .4em" >{{ subheader }}</div>
    </h1>
  </div>
</template>
<script>

export default {
  props: [
    'balance',
    'subheader'
  ]
}
</script>