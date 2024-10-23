<template>
    <!-- 
    В этом модуле содержиться заголовок каждого раздела. 
    Также он используется для вызова модуля описания раздела.
    Формат объекта с информацией о массиве:

    componentInfo: {
        name: 'string',
        incomes: ['string', 'string', 'string'],
        outcomes: ['string', 'string', 'string'],
        description: 'string',
        disclaimers: ['string', 'string', 'string']
      },
    
    
    Названия разделов передаются через пропсы 
    
    
    
    -->
  
    <div>
      <v-toolbar
        dark
        src='./images/autumnBg.jpg'
      >
        <!-- <v-app-bar-nav-icon/> -->
        <!-- <v-btn 
        icon
        >
          <v-icon>mdi-help</v-icon>
        </v-btn> -->

        <v-toolbar-title> {{this.title}} </v-toolbar-title>

        <v-spacer/>
        <v-btn 
        icon
        >
          <v-icon>mdi-information-outline</v-icon>
        </v-btn>
      </v-toolbar>
    </div>
  
    
  
  
  </template>
  
  <script>
  export default {
    name: "headerTab",
    data() {
      return {
        name: "name"
      };
    },
    props: {
        title: String,
    },
    methods: {
      
      
      //Старт
      start() {

      }, 
  
     
    },
  };
  </script>
  
  