<template>
    
  
    <div>
      <v-toolbar
        dark
        src='./images/autumnBg.jpg'
      >
        <v-app-bar-nav-icon/>

        <v-toolbar-title> {{this.title}} </v-toolbar-title>

        <!-- <v-spacer/>
        <v-btn 
        icon
        >
          <v-icon>mdi-export</v-icon>
        </v-btn> -->
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
  
  