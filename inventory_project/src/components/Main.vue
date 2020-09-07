<template>
    <div>
      <b-card title="Card Title" no-body>
        <b-card-header header-tag="nav">
          <b-nav card-header pills>
            <b-nav-item :active="inventory" @click="flip">Inventory</b-nav-item>
            <b-nav-item :active="!inventory" @click="flip">Production</b-nav-item>
          </b-nav>
        </b-card-header>

        <b-card-body v-if="inventory">
          <CurrentInventoryTable/>
        </b-card-body>
        <b-card-body v-else>
          <UpdateInventory/>
        </b-card-body>
      </b-card>
    </div>
</template>

<script>
import CurrentInventoryTable from './CurrentInventoryTable.vue'
import UpdateInventory from './UpdateInventory.vue'

export default {
  name: 'Main',
  components: {
    CurrentInventoryTable,
    UpdateInventory,
  },
  data() {
    return {
        inventory: true,
    };
  },
  computed: {
  },
  mounted() {
  },
  methods: {
      flip() {
          this.inventory = !this.inventory;
      }
  }
}
</script>
