<template>

  <v-data-table
    v-model="selected"
    :loading="loading"
    :headers="headers"
    :items="persons"
    item-key="potatoes"
    class="elevation-1 text-left"
    :items-per-page="1000"
    hide-default-footer
  >
  <template #body="props">
    <client-only>
      <draggable
            :list="props.items"
            tag="tbody"
            :disabled="!allowDrag"
            :move="onMoveCallback"
            :clone="onCloneCallback"
            @end="onDropCallback"
            @change="getCurrentOrder(props.items)"
          >
            <data-table-row-handler
              v-for="(item, index) in props.items"
              :key="index"
              :item="item"
              :headers="activeHeaders"
            >
            <template #item.lock="{ item }">
              <v-icon @click="item.locked = item.locked ? false : true">{{
                item.locked ? "mdi-checkbox-marked" : "mdi-checkbox-blank-outline"
              }}</v-icon>
            </template>
            <template #item.email="{ item }">
              {{ item.email }}
            </template>
            <template #item.tags="{ item }">
              <div v-if='Array.isArray(item.tags)'>
                  <v-chip
                  v-for='(tag, idx) in item.tags' :key='idx'
                    light
                  >
                    {{ tag }}
                  </v-chip>
              </div>
              <v-chip
                v-else
                    light
                  >
                  {{ item.tags }}
              </v-chip>
            </template>
            </data-table-row-handler>
          </draggable>
      </client-only>
  </template>
  </v-data-table>
</template>

<script>
  import draggable from "vuedraggable";
  import DataTableRowHandler from "./DataTableRowHandler.vue";
  export default {
    components: { 
      draggable, 
      DataTableRowHandler
     },
    props: {
      persons: {
        type: Array,
        required: true
      },
      loading: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        tries: 0,
        sortedArray: [],
        allowDrag: true,
        selected: [],
        headers: [
          { text: "", value: "lock", width: "10px", sortable: false },
          {
            text: 'Email',
            sortable: false,
            value: 'email',
          },
          { text: 'Potatoes', align: 'start', sortable: false, value: 'potatoes' },
          { text: 'Tags', sortable: false, value: 'tags', align: 'start' },
          { text: 'Full name', align: 'start', sortable: false, value: 'fullname' },
          { text: 'Location', align: 'start', sortable: false, value: 'location' },
          { text: 'Date / time', align: 'start', sortable: false, value: 'datetime' },
        ]
      }
    },
    watch: {
      persons(val) {
        if (val && val.length > 0) {
          this.sortedArray = val.map(el => parseInt(el.potatoes)).sort((a, b) => a - b).reverse()
        }
      }
    },
    computed: {
      activeHeaders() {
        return this.headers.filter((h) => {
          if (!this.allowDrag && h.value === "lock") {
            return false;
          }
          return true;
        });
      },
    },
    created() {
      this.tries = 0
    },
    methods: {
      getClass(item) {
        return item.potatoes > 500
          ? "cal-high"
          : item.potatoes > 400
          ? "cal-medium"
          : "cal-low";
      },
      onCloneCallback(item) {
        const cloneMe = JSON.parse(JSON.stringify(item))
        return cloneMe;
      },
      onMoveCallback(evt, originalEvent) {
        const item = evt.draggedContext.element;
        const itemIdx = evt.draggedContext.futureIndex;

        if (item.locked) {
          return false;
        }

        return true;
      },
      onDropCallback(evt, originalEvent) {
        this.tries++
      },
      getCurrentOrder(items) {
        let potatoes = []
        
        items.forEach(el => {
          potatoes.push(el.potatoes)
        })
        new Promise((resolve, reject) => {
          let potatoes = []
          items.forEach(el => {
            potatoes.push(el.potatoes)
          })
          resolve(potatoes)
        }).then((potatoes) => {
          const expectedOrder = JSON.stringify(this.sortedArray)
          const currentOrder = JSON.stringify(potatoes)

          // console.log('match', expectedOrder, currentOrder, expectedOrder == currentOrder)
          // console.log('potatoes', potatoes)
          // console.log('sorted', this.sorted(potatoes))

          if (expectedOrder == currentOrder) {
            this.$emit('is-sorted', true, this.tries)
            this.tries = 0
          }
        })
      },
      sorted(arr){
        let second_index;
        for(let first_index = 0; first_index < arr.length; first_index++){
            second_index = first_index + 1;
            if(arr[second_index] - arr[first_index] < 0) return false;
          }
          return true;
      }
    }
  }
</script>

<style lang="scss" scoped>
  .text-left {
    text-align: left;
  }
</style>

<style>
.v-chip {
  margin-right: 10px;
}
</style>