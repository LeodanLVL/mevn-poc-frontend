<script>
import { ref } from 'vue'

import FormClient from './FormClient.vue';



const idRef = ref(false);

const btn = "Update";

const idFunction = (id) => {
  idRef.value = id;

}
export default {

  data() {
    return {
      isShowFrom: false,
      idRef
    }
  },
  props: ["clients"],
  components: {
    FormClient
  },
  methods: {
    close() {
      this.isShowFrom = false

    },
    showForm() {
      this.isShowFrom = true

    }, idFunction
    //  ,
    //  refresh(clientsVar){
    //   this.clients = clientsVar;
    //  }

  },

}
  ;
</script>

<template>
  <!-- <Teleport to="#app"> -->

    
    
    <FormClient v-if="isShowFrom" @close="close" :id="idRef || false" :btn="`Update`" :clients="this.clients">
      
      
    </FormClient>
    <!-- </Teleport> -->
    
      <Teleport to="#app">

      
      <div class="container">
        
        <table>
          <thead>
            <th>Name</th>
            <th>Email</th>
            <th>Phone</th>
            <th>Providers</th>
            <th>Edit</th>
          </thead>
          
          <tbody>
            <tr v-for="client in clients" :key="client.id">
              <td>{{ client.name }}</td>
              <td>{{ client.email }}</td>
              <td>{{ client.phone }}</td>
              <td>
                <p v-for="provider in client.providers">{{ provider.name }}</p>
              </td>
              <td><button @click="idFunction(client.id); showForm()" :key="client.id">edit</button></td>
              
            </tr>
          </tbody>
          
          
        </table>
        
      </div>
      </Teleport>
    </template>



<style scoped>
table {
  display: block;
  margin: 2rem auto;
  width: 100%;
  max-width: 1000px;
  text-align: left;
}


th,
td {
  display: inline-block;
  padding: 10px;
}
.container{
  display: flex;
}
</style>