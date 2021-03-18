<template>
    <div>
        <div class="filter-list">
            <h2>Liste des filtres</h2>
            <div v-for="(value, key, index) in filters" :key="`filter-${index}-${key}-${value}`">
                <span>Filtre du "{{value.name}}"" avec "{{value.value}}"</span>
                <button @click="deleteFilter(index)">Supprimer Filtre</button>
            </div>
            <div>
                <span>Choix d'un filtre : </span>
                <select v-model="selected">
                    <option disabled value="">--Selectionner--</option>
                    <option v-for="option in options" :value="option" :key="`input-filter-${option.key}`">{{option.value}}</option>
                </select>
                <input v-model="newFilter"/>
                <button type="button" @click="addFilter" :disabled="!this.selected || !this.newFilter">Ajouter filtre</button>
            </div>
        </div>
        <table class="ticket-list">
            <thead class="header-list">
                <th @click="sort('firstName')">Prénom</th>
                <th @click="sort('lastName')">Nom</th>
                <th @click="sort('status')">Status</th>
                <th @click="sort('creationDate')">Creation</th>
                <th @click="sort('dueDate')">échéance</th>
                <th @click="sort('contactChannel')">Channel</th>
                <th @click="sort('assignedTo')">Assigné</th>
                <th>Dernier commentaire</th>
            </thead>
            <tbody>
                <tr v-for="(row, index) in filterAndOrderList" :key="`row-${index}`">
                    <td v-for="(value, key, index) in row" :key="`${key}-${index}`">
                        <span v-if="key === 'dueDate' || key === 'creationDate'">{{value | date}}</span>
                        <span v-else-if="key === 'firstName'">{{value| capitalize}} </span>
                        <span v-else-if="key === 'lastName'">{{value | uppercase}}</span>
                        <span v-else>{{value}}</span>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import dataList from "../../data/MOCK_DATA.json";
import moment from 'moment';

export default {
    name: 'TicketList',
    data() {
        return {
            items: dataList.map(data => {
                return {
                    firstName: data.customer.first_name.toLowerCase(),
                    lastName: data.customer.last_name.toLowerCase(),
                    status: data.status.toLowerCase(),
                    creationDate: data.interaction_creation_date,
                    dueDate: data.due_date,
                    contactChannel: data.contact_channel,
                    assignedTo: data.assignedTO,
                    lastComment: data.last_comment,
                }
            }, []),
            currentSort: '',
            currentSortDir: '',
            filters: [],
            newFilter: '',
            selected: '',
            options: [
                {
                    key: 'firstName',
                    value: 'Prénom'
                }, {
                   key: 'lastName',
                    value: 'Nom'    
                }, {
                    key: 'status',
                    value: 'status'
                }, {
                    key: 'assignedTo',
                    value: 'Assigné'
                }, {
                    key: 'contactChannel',
                    value: 'Channel'
                }    
            ]
        }
    },
    filters: {
        date: function(value) {
            return moment(value).locale('FR').format('Do MMMM YYYY HH:mm');
        },
        uppercase: function(value) {
            return value.toUpperCase();
        },
        capitalize: function(value) {
            return value.charAt(0).toUpperCase() + value.slice(1);
        }
    },
    computed: {
        filterAndOrderList() {
            return this.items.slice().sort((a, b) => {
                let modifier = 1;
                if(this.currentSortDir === 'desc') modifier = -1;
                if(a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
                if(a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
                return 0;
            })
            .filter((row) => {
                let ret = true;
                this.filters.forEach(filter => {
                    if (!row[filter.key].includes(filter.value.toLowerCase())) {
                        ret = false;
                        return ;
                    }
                })
                return ret;
            });
        }
    },
    methods: {
        sort: function(field) {
            if (this.currentSort === field) {
                this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc';
            }
            else {
                this.currentSortDir = 'asc';
            }
            this.currentSort = field;
        },
        addFilter: function() {
            this.filters.push({
                key: this.selected.key,
                name: this.selected.value,
                value: this.newFilter
            });
            this.newFilter = '';
            this.selected = '';
        },
        deleteFilter: function(index) {
            this.filters.splice(index, 1);
        }
    },
}
</script>

<style scoped>
    .filter-list {
        position: fixed;
        background-color: white;
        width: 100vw;
        z-index: 1;
        top: 0;
        padding-bottom: 20px;
        border-bottom: 1px solid black;
    }

    .ticket-list {
        position: relative;
        top: 150px;
    }


    table {
        border-collapse: collapse;
        margin: auto;
        border: 1px solid #CCC;
    }
    

    td {
        margin: 10px 0;
    }

    tr:nth-child(2n) {
        background-color: #EEE;
    }


    td > span {
        display: block;
        max-height: 3em;
        text-overflow: ellipsis;
        overflow: hidden;
        max-width: 30vw;
    }
</style>