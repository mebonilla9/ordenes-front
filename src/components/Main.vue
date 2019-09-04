<template>
  <div class="container-fluid">
    <div class="row justify-content-md-center">
      <div class="col-md-6">
        <div class="form-group">
          <select
            class="form-control form-control-sm"
            v-model="customers.customerId"
            @change="changeCustomer($event)"
          >
            <option :value="0">Seleccione...</option>
            <option
              v-for="cus in customers"
              :key="cus.customerId"
              :value="cus.customerId"
            >{{ cus.name }}</option>
          </select>
        </div>
      </div>
      <div class="col-md-1">
        <div id="loader" v-if="loading" class="spinner-border text-primary" role="status">
          <span class="sr-only">Loading...</span>
        </div>
      </div>
    </div>
    <div class="row justify-content-md-center">
      <div class="col-md-7">
        <div v-if="alert" class="alert alert-warning" role="alert">{{ dataMessage }}</div>
      </div>
    </div>
    <div class="row justify-content-md-center">
      <div class="col-md-10">
        <table class="table table-striped">
          <thead>
            <tr>
              <th scope="col">Creation Date</th>
              <th scope="col">Order ID</th>
              <th scope="col">Total "$"</th>
              <th scope="col">Delivery Address</th>
              <th scope="col">Products</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="ord in orders" :key="ord.orderId">
              <td>{{ ord.creationDate }}</td>
              <td>{{ ord.orderId }}</td>
              <td>{{ ord.total }}</td>
              <td>{{ ord.deliveryAddress }}</td>
              <td>
                <p v-for="det in ord.orderDetailList" :key="det.productId">{{ det.productId.name }}</p>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'Main',
  data () {
    return {
      loading: false,
      alert: false,
      customers: [],
      orders: []
    }
  },
  mounted () {
    this.loadCustomers()
  },
  methods: {
    loadCustomers () {
      axios
        .get('http://localhost:8090/ordersmg/api/customers/')
        .then(response => {
          this.customers = response.data
        })
    },
    changeCustomer (event) {
      this.loading = true
      this.alert = false
      axios
        .get(
          'http://localhost:8090/ordersmg/api/order/customer/' +
            event.target.value
        )
        .then(response => {
          this.loading = false
          if (response.data.length === 0) {
            this.dataMessage = 'No orders for this customer';
            this.alert = true
            this.orders = []
            return;
          }
          this.orders = response.data
        })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
