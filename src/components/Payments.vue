<template>
  <div class="hello" align="center">
    <h1>Payments Page</h1>
    <vue-table :columns="columns" :rows="payments" onChangeMe filterable>
      <template slot="Date" slot-scope="slotProps"><span>{{displayDate(slotProps.colData)}}</span></template>
      <template slot="Amount" slot-scope="slotProps"><span>{{usDollarFormatter(slotProps.colData)}}</span></template>
    </vue-table>
  </div>
</template>

<script>
import Vuex from 'vuex'
import moment from 'moment'
import db from '../helpers/firebaseConfig'
import VueTable from './VueTable.vue'
import Payments from '../../static/payments-data.csv'
import { usDollarFormatter } from '../util/formatMoney'

export default {
  name: 'PaymentsPage',
  data () {
    return {
      columns: [
        {
          header: 'Name',
          accessor: 'Name',
          minWidth: 120,
          sortMethod: function (a, b) { // custom sort by last name 
            if(a['Name'].split(' ')[1] > b['Name'].split(' ')[1]) { return 1 }
            if(a['Name'].split(' ')[1] < b['Name'].split(' ')[1]) { return -1 }
            return 0
          }
        },
        {
          header: 'Amount',
          accessor: 'Amount',
          minWidth: 150,
          customCell: true
        },
        {
          header: 'Description',
          accessor: 'Description',
          minWidth: 400,
          editable: true,
          onChange: (payment) => {
            this.updatePaymentDesc(payment)
          },
        },
        {
          header: 'Date',
          accessor: 'Date',
          minWidth: 120,
          customCell: true,
          filterMethod: function(rowValue, filterValue) { //custom filter on what the display date looks like MM/DD/YYYY
            const re = new RegExp(String(filterValue), 'i')
            const dateFormatted = moment(rowValue).format('MM/DD/YYYY')
            return String(dateFormatted).search(re) >= 0
          },
        }
      ]
    }
  },
  computed: Vuex.mapState(['payments']),
  methods: {
    createColumns () {

    },
    increment () {
      this.$store.dispatch('incrementAsync')
    },
    decrement () {
      this.$store.commit('decrement')
    },
    updatePaymentDesc (newPayment) {
      const originalPayment = this.payments.find(payment => payment.ID === newPayment.ID)
      if (originalPayment.Description !== newPayment.Description) {
        this.$store.dispatch('update_payment_description', newPayment)
      }
    },
    displayDate (date) {
      return moment(date).format('MM/DD/YYYY')
    },
    usDollarFormatter (amount) {
      return usDollarFormatter(amount)
    }
  },
  /*
  created () {
    this.$store.dispatch('setTodosRef', db.collection('todos'))
  }
  */
  created () {
    let paymentsRef = this.$store.state.firebaseApp.database().ref('payments')
    // create the payments if it doesn't exist
    paymentsRef.once('value', function (snapshot) {
      console.log(snapshot.val())
      if (!snapshot.exists()) {
        let dataObject = {}
        Payments.forEach(payment => {
          dataObject[payment.ID] = payment
        })
        paymentsRef.set(dataObject)
      }
    })
    this.$store.dispatch('setPaymentsRef', paymentsRef)
  },
  components: {
    VueTable: VueTable
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.counter {
  margin: 10px auto;
  border-radius: 3px;
  width: 200px;
  height: 200px;
  text-align: center;
  line-height: 200px;
  font-size: 5rem;
  background-color: #f0f0f0;
  user-select: none;
  margin-top: 20px;
}
h1, h2 {
  font-weight: normal;
}
</style>