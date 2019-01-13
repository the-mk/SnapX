<template>
  <div class="ui basic segment center aligned" v-if="transactions && blockBalance">
    <FloatedHeader title="Transactions" />
    <table class="ui celled table center aligned">
      <thead>
        <tr>
          <th></th>
          <th>Blocks to snapshot</th>
          <th>Amount (PAY)</th>
        </tr>
      </thead>
      <tbody>
      <tr v-for="transaction in transactions">
        <td data-label="Type">
          <a v-if="transaction.from == address" class="ui tiny orange label">OUT</a>
          <a v-if="transaction.to == address" class="ui tiny green label">IN</a>
        </td>
        <td data-label="Blocks to snapshot">{{ returnBlockDistance(transaction.blockNumber) }} </td>
        <td data-label="Amount (PAY)">{{ transaction.value / 1000000000000000000 }} </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import FloatedHeader from '~/components/FloatedHeader'

export default {
  components: {
    FloatedHeader
  },
  props: [
    'transactions',
    'snapshotBlock',
    'address',
    'blockBalance'
 ],
 data () {
  return {
    first: null,
  }
 },
 methods: {
    returnBlockDistance(block) {
      return block - this.snapshotBlock
    }
  },
}
</script>