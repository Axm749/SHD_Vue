<template>
  <v-app>
    <div 
      :class="$vuetify.theme.dark==false ? 'body_light' : 'body_dark mb-0'"
    >
      <!-- шапка при необходимости -->

      <!-- настройки -->
      <div 
        :class="noMargins === true ? 'ma-0' : 'mx-5 mb-10 module_bg'"
      >
        <v-expansion-panels
            accordion
            tile
          >
            <v-expansion-panel>
              <v-expansion-panel-header>
                <h2>Глобальные настройки</h2>
                <template v-slot:actions>
                  <v-icon>
                    mdi-cog
                  </v-icon>
                </template>
              </v-expansion-panel-header>
              <v-expansion-panel-content
              >
                <v-checkbox
                  info
                  hide-details
                  label="Использовать альтернативное отображение"
                  v-model="irregularView"
                  @change="saveTheme"
                />
                <v-checkbox
                  info
                  hide-details
                  label="Убрать отступы"
                  v-model="noMargins"
                  @change="saveTheme"
                />

                <v-switch
                  v-model="$vuetify.theme.dark"
                  inset
                  @change="saveTheme"
                  label="альтернативная тема"
                  persistent-hint
                />

              </v-expansion-panel-content>
            </v-expansion-panel>
          
        </v-expansion-panels>
      </div>
      
      <!-- основной вид -->
      <div 
        v-show="!irregularView"
        :class="noMargins === true ? 'ma-0' : 'ma-5'"
      >
        <div 
          :class="noMargins === true ? 'mt-2' : 'mt-2 module_bg'"
        >
          <shd
            @Power="getpower"
            @Usli="getUsli"
          />
        </div>
        
        <div 
          :class="noMargins === true ? 'mt-2' : 'mt-2 module_bg'"
        >
          <power
            ref="npower"
          />
        </div>
        
        <div 
          :class="noMargins === true ? 'mt-2' : 'mt-2 module_bg'"
        >
          <cooling
            ref="nusli"
          />
        </div>

        <div 
          :class="noMargins === true ? 'mt-2' : 'mt-2 module_bg'"
        >
          <vls
            ref="vls"
          />
        </div>
      </div>

      <!-- альтернативный вид -->
      <div 
        v-show="irregularView"
        :class="noMargins === true ? 'ma-0 mt-5' : 'ma-5 module_bg'"
      >
        <!-- добавить свойство v-model="panel" -->
        <v-expansion-panels
          multiple
          accordion
          tile
        >
          <!-- СХД -->
          <v-expansion-panel eager>
            <v-expansion-panel-header expand-icon="mdi-menu-down"><h2> Системы хранения данных </h2></v-expansion-panel-header>
            <v-expansion-panel-content>
              <div class="mx-n6">
                <shd
                  @Power="getpower"
                  @Usli="getUsli"
                />
                <v-spacer/>
                <power
                  ref="npower"
                />
                <cooling
                  ref="nusli"
                />
              </div> 
            </v-expansion-panel-content>
          </v-expansion-panel>

          <!-- Электропитание -->
          <!-- <v-expansion-panel eager>
            <v-expansion-panel-header><h2>Время работы оборудования от аккумуляторных батарей</h2></v-expansion-panel-header>
            <v-expansion-panel-content>
              <div class="mx-n6">
                <power
                  ref="npower"
                />
              </div>
            </v-expansion-panel-content>
          </v-expansion-panel> -->

          <!-- Охлаждение -->
          <!-- <v-expansion-panel eager>
            <v-expansion-panel-header><h2>Система кондиционирования</h2></v-expansion-panel-header>
            <v-expansion-panel-content>
              <div class="mx-n6">
                <cooling
                  ref="nusli"
                />
              </div>
            </v-expansion-panel-content>
          </v-expansion-panel> -->

          <!-- ВЛС -->
          <v-expansion-panel eager>
            <v-expansion-panel-header
              expand-icon="mdi-menu-down"
            >
              <h2>Опоры линии связи</h2>
            </v-expansion-panel-header>
            <v-expansion-panel-content>
              <div class="mx-n6">
                <vls
                  ref="vls"
                />
              </div>
            </v-expansion-panel-content>
          </v-expansion-panel>
          
        </v-expansion-panels>
      </div>
      
    </div>
  
  </v-app>
</template>

<script>

import power from './components/power.vue';
import shd from './components/shd.vue';
import cooling from './components/cooling.vue'
import vls from './components/vls.vue'


export default {
  name: 'App',

  components: {
    shd,
    power,
    cooling,
    vls    
  },

  data: () => ({
    // здесь разве что настройки
    irregularView: false,
    noMargins: true
  }),

  methods:{
    getpower(){
      this.$refs.npower.getPower()
    },
    getUsli(){
      this.$refs.nusli.getusli()
    },
    saveTheme(){
      localStorage.setItem('PD_VUE_DARK_THEME', this.$vuetify.theme.dark)
      localStorage.setItem('PD_VUE_NO_PADDING', this.noMargins)
      localStorage.setItem('PD_VUE_IRREGULAR_VIEW', this.irregularView)
      console.log('saved theme')
    }    
  },
  mounted(){
      if(localStorage.getItem('PD_VUE_DARK_THEME')=='true'){
        this.$vuetify.theme.dark = true
      } else {
        this.$vuetify.theme.dark = false
      }

      if(localStorage.getItem('PD_VUE_NO_PADDING')=='true'){
        this.noMargins = true
      } else {
        this.noMargins = false
      }

      if(localStorage.getItem('PD_VUE_IRREGULAR_VIEW')=='true'){
        this.irregularView = true
      } else {
        this.irregularView = false
      }



      // this.panel = []

    }
};
</script>

<style>

body{
  margin: 0;
  padding: 0px;
  width: 100%;
  height: 100%;
}

.body_light{
  /* background-image: linear-gradient( 0deg, #4e1600, #a07236); */

  background-image: linear-gradient( 0deg, #165f48, #275298);

  /* background-image: linear-gradient( 120deg, #33691E, #4DD0E1); */
  height: 100%;
  background-size: 200% 200%;
  animation: bg 5s ease infinite;
}

.body_dark{
  /* background-image: linear-gradient( 120deg, #000000, #2b0000); */
  background-image: linear-gradient( 0deg, #3e1100, #6c4d23);
  /* color: #111; */


  /* background-color: #000000; */
  height: 100%;
  background-size: 200% 200%;
  animation: bg 5s ease infinite;

  /* background-color: rgb(46, 46, 46); */
  /* color: #8f3900; */
  height: 100%;
}

.theme--dark.v-card{
  background-color: #000000;
  
  /* если захочу фон карточек сделать градиентом */
  /* background-image: linear-gradient( 120deg, #000000, #370000);
  background-size: 200% 200%;
  animation: bg 5s ease infinite; */
}

.theme--dark.v-expansion-panels .v-expansion-panel{
  background-color: #000;
}



@keyframes bg {
    0%{
        background-position: 0%, 100%;
        
    }
    50%{
        background-position: 100% 0%;
    }
    100%{
        background-position: 0%, 100%;
    } 
}


.container{
    padding: 20px;
    margin-top: 20px;
    width: 90%;
    border-radius: 20px;
    background-color:rgb(245, 244, 248);
}



input::-webkit-inner-spin-button, input::-webkit-outer-spin-button{
-webkit-appearance: none;
}

.module_bg{
  background-color: rgba(255, 255, 255, 0.3);
  border-radius: 0px;
  padding: 3px;
  margin-top: 20px;
  width: auto;
  /* box-shadow: 5px 5px 5px rgba(0, 0, 0, 0.127); */
  /* margin: 5px; */
}

.inCardTab{
  background-color: rgba(0, 0, 0, 0.03);
  border-radius: 5px;
  padding: 5px;
}


</style>
