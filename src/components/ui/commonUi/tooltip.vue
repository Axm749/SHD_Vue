<template>
    <div>
    
      <v-tooltip
        left
        max-width="80%"
        min-width="50%"
      >
        <template v-slot:activator="{ on }">
          <p>
            <v-icon small v-on="on">
                mdi-help-circle-outline
            </v-icon>
              {{ title }}     
          </p>
        </template>


        <!-- библиотека изображений -->
        <div>
          
          <v-img v-if="imageUrl=='autumnBg'" src="./images/autumnBg.jpg" />
          
          <v-img v-if="imageUrl=='forestBg'" src="./images/forestBg.jpg"/>

          <v-img v-if="imageUrl=='sunsetForestBg'" src="./images/sunsetForestBg.webp" />

        </div>

        <!-- если нет изображения -->
        <p class="text-center" v-if="imageUrl==''">Нет изображения</p>
          
        <!-- описание -->
        <p class="text-center" v-if="desc" > {{ desc }}</p>
        

      </v-tooltip>
    </div>

</template>

<script>
export default {
    name: "toolbarInfo",
    data() {
        return {          
        };
    },
    props: {
        title: String,
        imageUrl: String,
        desc: String,
    },
};
</script>
  
  