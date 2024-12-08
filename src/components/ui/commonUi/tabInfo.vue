<template>
    <!-- 
    В этом модуле высвечивается описание раздела.
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
    
    Информация из разделов передаются через пропсы, проходя через header.vue
    -->
  
  <div class="module_bg">
    <v-card tile>


      <!-- если информации нет -->
      <template v-if="!this.info">
        <p>there is no info... yet</p>
        <h2 class="text-center"> T~T </h2>
      </template>

      <!-- если информация есть -->
      <div v-if="this.info">
        <v-toolbar
          src="./images/sunsetForestBg.webp"
          dark
          flat
          prominent
        >
          <!-- <v-app-bar-nav-icon color="white"></v-app-bar-nav-icon> -->

          <v-toolbar-title>
            Информация о модуле
          </v-toolbar-title>
          
        </v-toolbar>
        <div class="pa-5">

          <h1>
            {{ info.name }}
          </h1>

          <h3 class="subheading">
            Описание
          </h3>
          <p>{{ info.description }}</p>

          

          <!-- входные параметры -->
          <div class="mt-5">
            <h3 class="subheading">
              входные параметры
            </h3>
            <li v-for="income in info.incomes" :key="income.incomes">
              {{ income }}
            </li>
          </div>

          <!-- выходные параметры -->
          <div class="mt-5">
            <h3 class="subheading">
              выходные параметры
            </h3>
            <li v-for="outcome in info.outcomes" :key="outcome.outcomes">
              {{ outcome }}
            </li>
          </div>

          <!-- важные замечания -->
          <div class="mt-5">
            <h3 class="subheading">
              Важные замечания
            </h3>
            <li v-for="disclaimer in info.disclaimers" :key="disclaimer.disclaimers">
              {{ disclaimer }}
            </li>
          </div>


          <!-- константы -->
          <div class="mt-5">
            <h3 class="subheading">
              Предустановленные значения
            </h3>
            <div v-for="constant in info.constants" :key="constant.constants">
              
              <h4>{{ constant["name"] }}</h4> 
               <!-- {{ constant["massive"] }} -->
               <div v-for="value in constant['massive']" :key="value">
                <li>
                  {{ value }}
                </li>
               </div>
               <br>
            </div>
            
          </div>

        </div>

        

      </div>    
    </v-card>

  </div>
</template>
  
<script>
export default {
  name: "tabInfo",
  data() {
    return {
      name: "name"
      
    };
  },
  props: {
      info: Object,
      // Info: {
      //   name: 'string',
      //   incomes: ['string', 'string', 'string'],
      //   outcomes: ['string', 'string', 'string'],
      //   description: 'string',
      //   disclaimers: ['string', 'string', 'string']
      // },
  },
  methods: {
    
    
    //Старт
    start() {

    }, 

    
  },
};
</script>
  
  