<template>
  <div>

    <ol class="breadcrumb">
      <li class="active">Apps</li>
    </ol>
    <br>
    <h1 class="page-header">
      Dashboard

      <div class="pull-right">
        <button class="btn btn-default" @click="openAddApp">
          <i class="fa fa-plus"></i>&nbsp;
          Create App
        </button>
      </div>
    </h1>

    <h3 class="page-header">Applications</h3>

    <div class="row">
      <div class="col-md-12 col-lg-10">
        <!-- <div class="table-responsive"> -->
        <div>
          <table class="table table-striped">
            <thead v-bind:class="{ transparent: !apps || apps.length == 0 }">
              <th>Name</th>
              <th width="120">Actions</th>
            </thead>
            <tbody>
              <tr v-for="app in apps">
                <td>
                  <router-link :to="'/app/' + encodeURIComponent(app.name)">{{app.name}}</router-link>
                </td>
                <td>
                  <div class="toolbar">

                    <div class="btn-group">
                      <button class="btn btn-default btn-sm" @click.prevent="openEditApp(app)" title="Edit App"><i class="fa fa-gear"></i> Edit</button>
                      <button type="button" class="btn btn-default dropdown-toggle btn-sm" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                        <span class="caret"></span>
                        <span class="sr-only">Toggle Dropdown</span>
                      </button>
                      <ul class="dropdown-menu dropdown-menu-right">
                        <li>
                          <a href="#" @click.prevent="deleteApp(app)" class="text-danger"  title="Delete App">
                            <i class="fa fa-times"></i> Delete App
                          </a>
                        </li>
                      </ul>
                    </div>

                  </div>
                </td>
              </tr>
              <tr v-if="apps && apps.length == 0">
                <td colspan="99" class="no-matches">
                  <div>No Apps</div>
                </td>
              </tr>
              <tr v-if="!apps">
                <td colspan="99" class="no-matches">
                  <div>Loading...</div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>


    <h3 class="page-header">
      Statistics
      <div class="pull-right">
        <label class="btn btn-default checkbox-inline" style="padding-left:30px"><!-- TODO style this properly -->
          <input type="checkbox" v-model="isChecked" @change="doalert">Auto refresh</label>
        </label>
      </div>
    </h3>
            
    <stats-chart :stats="stats" :statshistory="statshistory"></stats-chart>
    
    <fn-app-form></fn-app-form>
  </div>
</template>

<script>
import FnAppForm from '../components/FnAppForm';
import StatsChart from '../components/StatsChart'

import { defaultErrorHandler } from '../lib/helpers';
import { eventBus } from '../client';

export default {
  props: ['apps','stats','statshistory'],
  data () {
    return {
      isChecked: true,
    }
  },
  components: {
    FnAppForm,
    StatsChart
  },
  methods: {
    doalert: function(checkboxElem) {
      if (checkboxElem.currentTarget.checked) {
        eventBus.$emit('startAutoRefreshStats');
      } else {
        eventBus.$emit('stopAutoRefreshStats');
      }
    }, 
    openAddApp: function(){
      eventBus.$emit('openAddApp');
    },
    openEditApp: function(app){
      eventBus.$emit('openEditApp', app);
    },
    deleteApp: function(app){
      if (confirm('Are you sure you want to delete app ' + app.name + '?')) {
        var t = this;
        $.ajax({
          headers: {'Authorization': getAuthToken()},
          url: '/api/apps/' + encodeURIComponent(app.name),
          method: 'DELETE',
          dataType: 'json',
          success: (app) => { eventBus.$emit('AppDeleted', app) },
          error: defaultErrorHandler
        })
      }
    }
  }, 
  created: function (){
    document.title = "Functions UI";
    if (this.isChecked) {
      eventBus.$emit('startAutoRefreshStats');
    }
  }
}
</script>

<style>

</style>
