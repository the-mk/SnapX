<template>
  <div>
    <div class="ui padded basic segment" style="background-color: #001520">
      <HeaderElement />
    </div>
    <div class="ui grid container">
      <div class="column">
        <div class="ui basic center aligned segment">  
          <Notifications :message="message" />
          <div class="ui huge header">Enter your <b>public</b> address</div>
          <div class="ui big form" v-bind:class="{ 'loading' : querying}">
            <div class="field">
              <input ref="address" v-model="address" style="text-align: center; margin: 2em 0" type="text" placeholder="Ethereum Address">
            </div>
          </div>
          <Balance v-if="querySuccess" :balance="blockBalance" subheader="Balance at snapshot" />
          <button @click="addAddress()" class="ui right labeled icon blue button" v-if="Math.floor(blockBalance) > 0" style="background-color: #48d0e0">
            <i class="plus icon"></i>
            Add address
          </button> 
          <Transactions :snapshotBlock="snapshotBlock" :transactions="transactions" :address="address" :blockBalance="blockBalance" />
          <div class="ui basic segment center aligned" v-if="addresses.length">
            <FloatedHeader title="Addresses" />
            <Balance :balance="totalBalance" v-if="totalBalance" subheader="Total balance at snapshot" />
            <AddressTable :addresses="addresses" />
          </div>
        </div>
      </div>

    </div>
    <FooterElement />
  </div>
</template>

<script>
import axios from 'axios'
import Transactions from '~/components/Transactions'
import HeaderElement from '~/components/HeaderElement'
import Notifications from '~/components/Notifications'
import Balance from '~/components/Balance'
import AddressTable from '~/components/AddressTable'
import FooterElement from '~/components/FooterElement'
import FloatedHeader from '~/components/FloatedHeader'

export default {
  components: {
    Transactions,
    HeaderElement,
    Notifications,
    Balance,
    AddressTable,
    FooterElement,
    FloatedHeader
  },
  data () {
    return {
      API_KEY: process.env.API_KEY,
      transactions: null,
      address: '',
      balance: null,
      blockBalance: null,
      totalBalance: 0,
      querying: false,
      querySuccess: false,
      message: null,
      snapshotBlock: 6980194,
      inputError: false,
      addresses: []
    }
  },
  mounted () {
  },
  methods: {
    returnBlockDistance(block) {
      return block - this.snapshotBlock
    },
    setDefault () {
      this.querying = false
      this.querySuccess = false,
      this.message = null,
      this.balance = null,
      this.blockBalance = null
      this.inputError = false
      this.$refs.address.focus()
    },
    getAddressData () {
      this.setDefault()
      this.querying = true
      axios
        .get('https://api.etherscan.io/api?module=account&action=tokentx&contractaddress=0xB97048628DB6B661D4C2aA833e95Dbe1A905B280&address=' + this.address + '&sort=asc&apikey=' + this.API_KEY)
        .then(response => (this.handleResponse(response)))
    },
    handleResponse(response) {
      this.transactions = response.data.result
      this.checkQuerySuccess(response.data.status)
      this.querying = false
      this.getBalanceTilBlock(this.snapshotBlock)    
    },
    checkQuerySuccess (status) {
      if (status == 1) {
        this.querySuccess = true
      } else {
        this.querySuccess = false
      }
    },
    getBalanceTilBlock (block = null) {
      if (this.querySuccess) {
        this.balance = 0;
        for (let index = 0; index < this.transactions.length; ++index) {
            let transaction = this.transactions[index]
            if (transaction.from == this.address) {
              this.balance -= parseInt(transaction.value)
            } else if (transaction.to == this.address) {
              this.balance += parseInt(transaction.value)
            }
            if (block) {
              if (transaction.blockNumber <= block) {
                this.blockBalance = this.balance
              }
            }
        }
        this.balance = this.balance/(1000000000000000000)
        this.blockBalance = this.blockBalance/(1000000000000000000)
      } else {
        this.inputError = true
        this.message = 'No transactions of the PAY token found on this address'
        this.$refs.address.focus()
      } 
    },
    addAddress () {
      if (this.blockBalance > 0) {
        for (var i = this.addresses.length - 1; i >= 0; i--) {
          if (this.address == this.addresses[i].address) {
            this.message = 'Address already added'
            return
          }
        }
        this.addresses.push(
          {
            address : this.address,
            amount: this.blockBalance
          })
        this.totalBalance += this.blockBalance
        this.setDefault()
        this.address = ''
        this.$refs.address.focus()
      } else {
        this.message = 'No PAY on this address at snapshot time'
        this.$refs.address.focus()
      }
    },
  },
  watch: {
    address () {
      this.address = this.address.toLowerCase().trim()
      if (this.address.length != 42 && this.address.length > 0) {
        this.setDefault()
        this.inputError = true
        this.message = 'No ethereum address detected.'
        this.$refs.address.focus()
      } else {
        if (this.address.length == 42 && (this.querying == false)) {
          this.getAddressData()
        } else if (this.querying == false) {
          this.setDefault()
        }     
      }

    }
  },
}
</script>

<style>
</style>
