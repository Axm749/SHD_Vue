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
        disclaimers: ['string', 'string', 'string'],
        constants: [
          { name: 'string:', 
            massive: [ 'string', 'string', 'string' ]
          },
          { name: 'string:', 
            massive: [ 'string', 'string', 'string' ]
          }
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
        @click="dialog=!dialog"
        >
          <v-icon>mdi-information-outline</v-icon>
        </v-btn>
      </v-toolbar>


        <v-dialog
            v-model="dialog"
            novalidate
            width="auto"
            aria-hidden="true"
        >
            <tabInfo v-bind:info = "info"></tabInfo>

        </v-dialog>
    </div>
  
    
  
  
</template>

<script>
import tabInfo from './tabInfo.vue';

export default {
    name: "headerTab",
    components: {
        tabInfo,
    },
    data() {
        return {
        name: "name",
        dialog: false,

        };
    },
    props: {
        title: String,
        info: Object
    },
};
</script>
  
  