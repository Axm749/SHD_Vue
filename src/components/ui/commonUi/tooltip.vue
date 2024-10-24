<template>
    <div>
    
      <v-tooltip
        left
        max-width="350px"
        min-width="400px"
      >
        <template v-slot:activator="{ on }">
          <p>
            <v-icon small v-on="on">
                mdi-help-circle-outline
            </v-icon>
              {{ title }}     
          </p>
        </template>


        <div>
          <v-img v-if="imageUrl=='autumnBg'" src="./images/autumnBg.jpg" />
          
          <v-img v-if="imageUrl=='forestBg'" src="./images/forestBg.jpg"/>

          <v-img v-if="imageUrl=='sunsetForestBg'" src="./images/sunsetForestBg.webp" />
        </div>
        

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
    },
};
</script>
  
  