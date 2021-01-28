<template>
    <b-container class="">
        <b-form-group
          label="Filter"
          label-for="filter-input"
        >
          <b-input-group size="sm">
            <b-form-input
              id="filter-input"
              v-model="filterString"
              type="search"
              placeholder="Type to Search"
            ></b-form-input>

            <b-input-group-append>
              <b-button @click="filterString = ''">Clear</b-button>
            </b-input-group-append>
          </b-input-group>
        </b-form-group>
        <b-form-group
          label="Filter On"
          description="Leave all unchecked to filter on all data"
          v-slot="{ ariaDescribedby }"
        >
          <b-form-checkbox-group
            v-model="filterOn"
            :aria-describedby="ariaDescribedby"
            class="mt-1"
          >
            <b-form-checkbox value="first_name">First Name</b-form-checkbox>
            <b-form-checkbox value="last_name">Last Name</b-form-checkbox>
          </b-form-checkbox-group>
        </b-form-group>
        <b-form-checkbox v-model="registeredOnly" >Show registered only</b-form-checkbox>
        <b-table
            :items="items"
            :fields="fields"
            primary-key="id"
            striped
            :current-page="currentPage"
            :per-page="perPage"
            :filter="filter"
            :filter-function="filterFunction"
            stacked="md"
            @filtered="onFiltered"
            class="mt-3"
        >

            <template #cell(is_registered)="data"><b-form-checkbox :checked="data.item.is_registered" @change="toggleRegistered(data)"><span class="sr-only">Registered?</span></b-form-checkbox></template>
            <template #cell(favorite_color)="data"><div class="swatch" :style="{ '--bg-color': data.item.color }">{{data.item.color}}</div></template>
        </b-table>

        <b-pagination
          v-model="currentPage"
          :total-rows="totalRows"
          :per-page="perPage"
        ></b-pagination>
    </b-container>
</template>

<script>
import items from '../../MOCK_DATA.json'
export default {
    name: 'HelloWorld',
    props: {
        msg: String,
    },
    data() {
        return {
            items,
            fields: [
                {
                    key: 'is_registered',
                    label: 'Registered?',
                    sortable: true,
                },
                {
                    key: 'first_name',
                    sortable: true,
                },
                {
                    key: 'last_name',
                    sortable: true,
                },
                {
                    key: 'email',
                },
                {
                    key: 'company',
                    sortable: true,
                },
                {
                    key: 'favorite_color',
                },

            ],
            totalRows: 1,
            currentPage: 1,
            perPage: 10,
            filterString: '',
            filterOn: [],
            registeredOnly: false,
            sortDirection: 'asc',

        };
    },
    computed: {
        filter() {
            if (this.filterString || this.registeredOnly) {
                return {
                    filterString: this.filterString,
                    registeredOnly: this.registeredOnly,
                }
            }   else {
                return null;
            }
        },
    },
    mounted() {
        // Set the initial number of items
        this.totalRows = this.items.length
    },
    methods: {
        toggleRegistered(data) {
            data.item.is_registered = !data.item.is_registered;
        },
        changeRow(index, data) {
            this.items[index] = {
                ...this.items[index],
                ...data,
            };
        },
        onFiltered(filteredItems) {
        // Trigger pagination to update the number of buttons/pages due to filtering
            this.totalRows = filteredItems.length
            this.currentPage = 1
        },
        filterFunction(record) {
            if ( this.registeredOnly && !record.is_registered ) {
                console.log(record.is_registered)
                return false;
            }
            if ( this.filterString) {
                if (this.filterOn.length) {
                    return this.filterOn.some(field => record[field].toLowerCase().includes(this.filterString.toLowerCase()))
                } else {
                    return Object.values(record).some(val => val && typeof val === 'string' && val.toLowerCase().includes(this.filterString.toLowerCase()))
                }
            }
            return true;
        },
    },
}
</script>
<style>
.swatch {
    background-color: var(--bg-color);
}
</style>
