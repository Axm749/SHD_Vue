<template>
  <div
  >
    <!-- заголовок -->
    <div class="header">
      <headerTab title="Воздушные линии связи"></headerTab>
    </div>


    <!-- ввод требований -->
    <v-card 
      tile
      class="pa-5" 
    >
      <!-- <h1>Воздушные линии связи</h1> -->
        
      <!-- старт -->
      <v-btn 
        @click="start" 
        color="primary" 
        class="mt-5"
      >Старт</v-btn>
    </v-card>


    

    <!-- уведомление об ошибке -->
    <v-snackbar
      v-model="snackbar"
      :timeout="timeout"
    >
      {{ errorText }}

      <template v-slot:action="{ attrs }">
        <v-btn
          color="accent"
          text
          v-bind="attrs"
          @click="snackbar = false"
        >
          OK
        </v-btn>
      </template>
    </v-snackbar>

  </div>

  


</template>

<script>
import headerTab from './ui/commonUi/header.vue';
export default {
  name: "vls_vue",
  components: {
    headerTab,
  },
  data() {
    return {
      snackbar: false,      // окошко об ошибке
      timeout: 2500,        // время высвечивания окна об ошибке
      errorText: 'Неверно введены данные или они отсутствуют',
    };
  },
  methods: {
    
    
    //Старт
    start() {

    }, 

    
  },
};
</script>

