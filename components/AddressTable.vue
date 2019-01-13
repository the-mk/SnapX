<template>
  <table class="ui definition table center aligned">
    <tbody>
      <tr v-for="address in addresses" :key="address.address">
        <td >{{ address.address }}</td>
        <td class="four wide column">{{ address.amount.toLocaleString('en') }} PAY</td>
      </tr>
    </tbody>
  </table>
</template>
<script>

export default {
  props: [
    'addresses'
  ]
}
</script>