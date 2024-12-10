<template>
    <div>
    
      <v-tooltip
        right
        max-width="300px"
        min-width="40%"
      >
        <template v-slot:activator="{ on }">
          <v-row>
            <v-col class="mr-n5">
              <v-icon small v-on="on">mdi-help-circle-outline
              </v-icon>
            </v-col>
            <v-col>
              {{ title }} 
            </v-col>
               
          </v-row>
        </template>


        <!-- библиотека изображений -->
        <div>
          <!-- <v-img  
            :src="`@/components/ui/commonUi/images/${imageUrl.toString()}`" 
          />
           -->
          <!-- общие картинки -->
          <v-img v-if="imageUrl=='autumnBg'" src="./images/theme/autumnBg.jpg" />
          
          <v-img v-if="imageUrl=='forestBg'" src="./images/theme/forestBg.jpg"/>

          <v-img v-if="imageUrl=='sunsetForestBg'" src="./images/theme/sunsetForestBg.webp" />

          <!-- подсказки для видеонаблюдения -->

          <v-img 
            v-if="imageUrl=='verticalFOV'" 
            src="./images/cameraTips/Вертикальный_угол_обзора.png" 
          />

          <v-img 
            v-if="imageUrl=='horizontalFOV'" 
            src="./images/cameraTips/Горизонтальный_уол_камеры.png" 
          />

          <v-img 
            v-if="imageUrl=='placementHeight'" 
            src="./images/cameraTips/Высота_установки_камеры.png" 
          />

          <v-img 
            v-if="imageUrl=='blindZone'" 
            src="./images/cameraTips/Длина_Слепой_Зоны.png" 
          />
          
          <v-img 
            v-if="imageUrl=='PPMZones'" 
            src="./images/cameraTips/Зоны_PPM.png" 
          />

          <v-img 
            v-if="imageUrl=='cameraTilt'" 
            src="./images/cameraTips/Угол_наклона_камеры.png" 
          />

          <v-img 
            v-if="imageUrl=='viewDistance'" 
            src="./images/cameraTips/Максимальная_длина_обзора_камеры.png" 
          />


          <!-- VLS tips -->


          <v-img 
            v-if="imageUrl=='strelaProvesa'" 
            src="./images/vlsTips/strelaProvesa.jpg" 
          />

          <v-img 
            v-if="imageUrl=='perepadVisot'" 
            src="./images/vlsTips/perepadVisot.jpg" 
          />

        </div>



        <!-- если нет изображения -->
        <p class="text-center" v-if="imageUrl==''">Нет изображения</p>
          
        <!-- описание -->
        <p class="text-center" v-if="desc" > {{ desc }}</p>
        

      </v-tooltip>
    </div>

</template>

<script>
// import a from '@/components/ui/commonUi/images/vlsTips/'

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
  
  