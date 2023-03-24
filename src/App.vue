<script>
import { ref } from 'vue'

import FormClient from './components/FormClient.vue'
import Table from './components/Table.vue'


const url = "http://localhost:4000/api/clients"


export default {
  data() {
    return {
      isShowFrom: false,
      clients: []
    }
  },
  async mounted() {
    try {
      await fetch(url)
        .then((resp) => {
          return resp.json();
        }).then(({ data }) => {
          // console.log(data);
          this.clients = [...data]
        });

    } catch (error) {
      console.log(error);
    }
  },
  components: {
    FormClient,
    Table
  },
  methods: {
    close() {
      this.isShowFrom = false

    },
    showForm() {
      this.isShowFrom = true
    },
    // refresh(clientsVar) {
    //   this.clients.value = clientsVar;
    // }
  }
};
</script>


<template>
  <!-- Conditional Modal -->
  
  <!-- Header -->
  
  <FormClient v-if="isShowFrom" @close="close" :id="false" :btn="`Insert`" :clients="clients">
  </FormClient>
  <Table :clients="clients"></Table>
  

      <header class="container">
        <h1>Clients</h1>
        <button class="btn" @click="showForm">New Client</button>
      </header>
      
     
    
    <!-- Table -->
    

</template>


<style>
  
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  font-family: Arial, Helvetica, sans-serif;
}

body {
  padding: 1rem;
  width: 100vw;
  height: 100vh;
  background-color: #f2f2f2;
}

header {
  display: flex;
  align-items: center;
  justify-content: space-between; 
  
}

header .btn {
  padding: 0.5rem 1rem;
  border-radius: 5px;
  font-weight: bold;
  box-shadow: 4px 4px 5px rgba(0, 0, 0, 0.5);
  font-size: 1.3rem;
  box-sizing: border-box;
}

header .btn:hover {
  cursor: pointer;
  background-color: black;
  color: whitesmoke;

}
</style>