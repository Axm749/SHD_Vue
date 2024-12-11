<!-- 
 
  Микромодуль для показа подсказок.
  Какую в него передавать информацию (пропсы)


  title: String,    - то, что будет написано рядом со знаком вопроса
  
  desc: String,     - текстовое описание, которое нужно вывести под изображением или вместо него

  imgRole:String,   - нужна для понимания, о какой категории (директории) речь

  tipUrl: String,   - название самого файла в категории

  srcUrl: String,   - если есть способ вбить адрес изображения напрямую (например ссылка)

  imageUrl: String, - уже не актуальная штука, нужно будет переделать

-->
<template>
  <div>
      <v-tooltip
        right
        max-width="300px"
        min-width="40%"
        color="primary"
      >
        <template v-slot:activator="{ on }">
              <v-icon small v-on="on">mdi-help-circle-outline
              </v-icon>
              {{ title }} 
        </template>


        <!-- img by role & name -->
        <div v-if="tipUrl">
          <v-img
            :src="require(`${getImgUrl}`)" 
            aspect-ratio="1"
            gradient="to top right, rgba(100,115,201,.1), rgba(0,0,0,.1)"
          >
            <template v-slot:placeholder>
              <v-row
                class="fill-height ma-0"
                align="center"
                justify="center"
              >
                <v-progress-circular
                  indeterminate
                  color="grey lighten-5"
                ></v-progress-circular>
              </v-row>
            </template>
          </v-img>
        </div>

        <!-- img by raw src -->
        <div v-if="srcUrl">
            <v-img
              :src="require(`${srcUrl}`)" 
              aspect-ratio="1"
              gradient="to top right, rgba(100,115,201,.1), rgba(0,0,0,.1)"
            >
              <template v-slot:placeholder>
                <v-row
                  class="fill-height ma-0"
                  align="center"
                  justify="center"
                >
                  <v-progress-circular
                    indeterminate
                    color="grey lighten-5"
                  ></v-progress-circular>
                </v-row>
              </template>
            </v-img>
          </div>




        <!-- библиотека изображений -->
        <div>
   
          <!-- общие картинки -->
          <v-img v-if="imageUrl=='autumnBg'" src="./images/theme/autumnBg.jpg" />
          
          <v-img v-if="imageUrl=='forestBg'" src="./images/theme/forestBg.jpg"/>

          <v-img v-if="imageUrl=='sunsetForestBg'" src="./images/theme/sunsetForestBg.webp" />

          
          <!-- подсказки для видеонаблюдения -->

          <v-img 
            v-if="imageUrl=='verticalFOV' "  
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
          defaultImage: 'autumnBg',
          imageRoles: [

          ],
        };
    },
    props: {
        title: String,

        imageUrl: String, // уже не актуальная штука, нужно будет переделать

        imgRole:String,

        tipUrl: String,

        srcUrl: String,

        desc: String,
    },
    computed: {
      getImgRole (){
        if (this.imgRole == 'vlsTips' ) return './images/vlsTips/'
        // для подсказок раздела ВЛС
        else if (this.imgRole == 'cameraTips' ) return './images/cameraTips/'
        // для подсказок раздела видеонаблюдения
        return './images/theme/'
        // if no role, then default
      },
      getImgUrl () {
        return `${this.getImgRole}${this.tipUrl}`
      }
    }
};
</script>
  
  