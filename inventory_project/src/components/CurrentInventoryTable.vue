<template>
  <div>
    <h1 class="my-5">Current Inventory Table</h1>
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
  name: 'CurrentInventoryTable',
  data() {
    return {
        fields: [
          {
            key: 'inventoryID',
            thClass: 'd-none', 
            tdClass: 'd-none',
          },
          'name',
          'inventoryValue',
        ],
        tableData: [],
        rendered: false,
        host: process.env.VUE_APP_INVENTORY_MANAGEMENT_HOST,
    };
  },
  computed() {
    return {
      rowData() {
        const rows = [];
        this.tableData.array.forEach((value) => {
          const row = {
            inventoryID: value.inventoryID,
            name: value.name,
            inventoryValue: value.inventoryValue,
          }
          rows.append(row);
        });
        return rows;
      }
    }
  },
  mounted() {
    axios({
        method: 'get',
        url: `${this.host}/api/inventoryManagment/currentInventory`,
        crossdomain: true,
    })
    .then((res) => {
        // console.log(res);
        this.tableData = res.data.data;
    });
    this.rendered = true;
  }
}
</script>

<style scoped>
.table {
  margin: auto;
  widows: 90%;
}
</style>
