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
    <b-table
      :busy="!rendered"
      :field="fields"
      :items="tableData"
      striped hover bordered
      class="mx-auto w-75 mb-5"
      >
      <template v-slot:table-busy>
          <div class="text-center text-primary my-2">
            <b-spinner class="align-middle"></b-spinner>
            <strong>Loading...</strong>
          </div>
        </template>

        <template v-slot:cell(name)="data">
          <div class="text-left"><b>{{ data.value }}</b></div>
        </template>

        <template v-slot:cell(nameage)="data">
          {{ data.item.name.first }} is {{ data.item.age }} years old
        </template>

    </b-table>
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
        inventoryObject: {},
        fields: [
          {
            key: 'productionID',
            thClass: 'd-none', 
            tdClass: 'd-none',
          },
          'inventoryItem',
          'dateTime',
          'quantity',
        ],
        tableData: [],
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
        rows.forEach((value) => {
          const id = value.inventoryID;
          this.inventoryObject[id] = value.name;
        })
        const dropdown = rows.map((value) => {
            return {text: value.name, value: value.inventoryID};
        });
        // console.log(dropdown);
        dropdown.forEach(element => {
            this.items.push(element);
        });
    });
    axios({
        method: 'get',
        url: `${this.host}/api/inventoryManagment/getProducedItems`,
        crossdomain: true,
    })
    .then((res) => {
        const production = res.data.data;
        // console.log(JSON.stringify(production));

        const productionData = [];
        production.forEach((value) => {
          productionData.push({
            productionID: value.productionID,
            dateTime: value.dateTime,
            item: this.inventoryObject[value.inventoryID],
            quantity: value.quantity,
          })
        })
        // console.log(JSON.stringify(productionData))
        this.tableData = productionData;
        this.rendered = true;
    });
  },
  methods: {
      async onSubmit() {
        // console.log('checking inventory');
        // console.log(JSON.stringify(this.form));
        const inventoryID = this.form.id;
        const quantity = this.form.quantity;

        // if (!this.isPositive(inventoryID, true)) return;
        if (!this.isPositive(quantity, false)) return;

        const req = {
            data: {
                inventoryID,
                quantity: parseFloat(quantity),
            },
        };
        // console.log(JSON.stringify(req));
        /*eslint-disable */
        try {
          const config = {
            method: 'post',
            url: `${process.env.VUE_APP_INVENTORY_MANAGEMENT_HOST}/api/inventoryManagment/produceItem`,
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
