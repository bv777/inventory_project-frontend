<template>
  <div>
    <h1 class="my-5">Production</h1>
    <!-- <b-form @submit="onSubmit" v-if="show"> -->
    <b-form class="w-75 m-auto" v-if="rendered">
      <b-form-group 
        id="input-group-1" 
        label-cols-sm="4" label-cols-lg="3" 
        label="Inventory Item" label-for="input-1">
        <b-form-select
          id="input-1"
          v-model="form.id"
          :options="items"
          required
        ></b-form-select>
      </b-form-group>

      <b-form-group
        id="input-group-2"
        label-cols-sm="4" label-cols-lg="3"
        label="Amount Produced" label-for="input-2">
        <b-form-input 
          :id="`input-2`"
          :state="quantityStatus"
          v-model="form.quantity"
          aria-describedby="input-quantity"
        />
        <b-form-invalid-feedback id="input-quantity">
            Enter a postive number.
        </b-form-invalid-feedback>
      </b-form-group>

      <b-button type="submit" variant="primary" @click="onSubmit">Submit</b-button>
    </b-form>
    <!-- <b-card class="mt-3" header="Form Data Result">
      <div class="text-left"><pre class="m-0">{{ form }}</pre></div>
    </b-card> -->
  </div>
</template>

<script>
// import Vuetable from 'vuetable-2'
import axios from 'axios';

export default {
  name: 'UpdateInventory',
  data() {
    return {
        host: process.env.VUE_APP_INVENTORY_MANAGEMENT_HOST,
        form: {
            id: null,
            quantity: null,
        },
        items: [{ text: 'Choose...', value: null }],
        rendered: false,
        inventory: [],
        last: {
            id: null,
            quantity: null,
        },
    };
  },
  computed: {
      quantityStatus() {
          const quantity = this.form.quantity;
          return this.isPositive(quantity, true);
      },
  },
  mounted() {
    axios({
        method: 'get',
        url: `${this.host}/api/inventoryManagment/currentInventory`,
        crossdomain: true,
    })
    .then((res) => {
        const rows = res.data.data;
        this.inventory = rows;
        const dropdown = rows.map((value) => {
            return {text: value.name, value: value.inventoryID};
        });
        // console.log(dropdown);
        dropdown.forEach(element => {
            this.items.push(element);
        });
        this.rendered = true;
    });
  },
  methods: {
      async onSubmit() {
        // console.log('checking inventory');
        const inventoryID = this.form.id;
        const quantity = this.form.quantity;

        if (!this.isPositive(inventoryID, true)) return;
        if (!this.isPositive(quantity, false)) return;

        const item = this.inventory.filter(value => (value.inventoryID == inventoryID))[0];
        // console.log(JSON.stringify(item));
        const req = {
            data: {
                inventoryID,
                name: item.name,
                unitPrice: parseFloat(item.unitPrice),
                quantity: parseFloat(quantity) + parseFloat(item.quantity),
                minimalSupply: parseFloat(item.minimalSupply),
            },
        };
        console.log(req);
        /*eslint-disable */
        try {
          const config = {
            method: 'put',
            url: `${process.env.VUE_APP_INVENTORY_MANAGEMENT_HOST}/api/inventoryManagment/updateInventoryItem`,
            headers: { 
              'Content-Type': 'application/json'
            },
            data : JSON.stringify(req),
            crossdomain: true,
          };
          let res = await axios(config);
        } catch (e) {}
        /*eslint-enable */
      },
      isPositive(num, floatType) {
          if (num === null) return false;
          var x = -1;
          if (floatType) {
            x = parseFloat(num);
          } else {
            x = parseInt(num);
          }
          if (isNaN(x) || x <= 0) {
              return false;
          }
          return true;
      }
  }
}
</script>
