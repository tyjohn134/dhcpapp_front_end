<template>
     <v-app>
      <v-content>
        <v-container :fluid="true">
               <v-alert color="success" icon="error" dismissible v-model="alert" v-if="loading_data">
                    Loading data...
              </v-alert>
           <v-card>
              <v-card-title>
                <h5 class="headline">DHCP App - Last Run: {{date_now}}</h5>
                <v-spacer></v-spacer>
                <v-text-field
                  append-icon="search"
                  label="Search"
                  single-line
                  hide-details
                  v-model="search"
                ></v-text-field>
                <v-card-title>
                </v-card-title>
              </v-card-title>
              <v-data-table
                  v-bind:headers="headers"
                  v-bind:items="items"
                  v-bind:search="search"
                  v-bind:pagination.sync="pagination"
                  :rows-per-page-items="[15, 25,50, {text:'All', value:-1 }]"
                >
                <template slot="items" slot-scope="props">
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}"> {{ props.item.scope }} </td>
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.percinuse }}</td>
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.reserved }}</td>
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.dur }}</td>
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.subnet }}</td>
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.strange }}</td>
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.endrange }}</td>
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.inuse }}</td>
                  <td  v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.free }}</td>
                  <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.state }}</td>
                   <td v-bind:class="{ 'red lighten-3': (props.item.percinuse >= 90)}" class="text-xs-right">{{ props.item.scopeip }}</td>
                </template>
                <template slot="pageText" slot-scope="{ pageStart, pageStop }">
                  From {{ pageStart }} to {{ pageStop }}
                </template>
              </v-data-table>
          </v-card>

        </v-container>
      </v-content>
    </v-app>
</template>

<script>
import axios from 'axios'
import moment from 'moment'
  export default {
    data () {
      return {
         max25chars: (v) => v.length <= 25 || 'Input too long!',
        tmp: '',
        search: '',
        alert: '',
        date_now: '',
        loading_data: true,
        pagination: {
          sortBy: 'percinuse',
          descending: true
        },
        
        headers: [
          {
            text: 'Name',
            align: 'left',
            value: 'scope'
          },
          { text: '% In Use', value: 'percinuse' },
          { text: 'Reserved', value: 'reserved' },
          { text: 'Lease Duration', value: 'dur' },
          { text: 'Subnet Mask', value: 'subnet' },
          { text: 'Start Range', value: 'strage' },
          { text: 'End Range', value: 'endrange' },
          { text: 'In Use', value: 'inuse' },
          { text: 'Free', value: 'free' },
          { text: 'State', value: 'state' },
          { text: 'IP Scope', value: 'scopeip' }
        ],
        items: []
      }
    },
    methods: {
      loadData: function() {
        this.loading_data = true;
        axios.get('http://xx.xx.xx.xx/api/data')
        .then(response => {
          // JSON responses are automatically parsed.
          this.items = response.data;
          this.loading_data = false;
          this.date_now = moment().format('MM/DD/YYYY h:mm a');
        })
        .catch(e => {
          this.errors.push(e)
        })
      },
    },

    created() {
      this.loadData();
      setInterval(function () {
        this.loadData();
      }.bind(this), 6000);
    },


  }
</script>

<style>
.lightred {
  background-color: #FA8072;
}
</style>