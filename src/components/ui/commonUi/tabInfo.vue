<template>
    <!-- 
    В этом модуле высвечивается описание раздела.
    Формат объекта с информацией о массиве:

    componentInfo: {
        name: 'string',
        incomes: ['string', 'string', 'string'],
        outcomes: ['string', 'string', 'string'],
        description: 'string',
        disclaimers: ['string', 'string', 'string']
      },
    
    
    Информация из разделов передаются через пропсы, проходя через header.vue
    
       
    -->
  
  <div class="module_bg">
    <v-card 
    class="pa-5"
    tile
    >
      <v-card-title>
        Информация о разделе
      </v-card-title>

      <!-- если информации нет -->
      <template v-if="!this.info">
        <p>there is no info... yet</p>

      </template>



      {{ this.info }}
    
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
  },
  methods: {
    
    
    //Старт
    start() {

    }, 

    
  },
};
</script>
  
  