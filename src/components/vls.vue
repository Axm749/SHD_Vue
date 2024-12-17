<template>
  <div
  >
    <!-- заголовок -->
    <div class="header">
      <headerTab title="Расчет опор линии связи" v-bind:info="componentInfo"></headerTab>
    </div>


    <!-- ввод требований -->
    <v-card 
      tile
      flat
      class="pa-5" 
    >
      <!-- проверка подсказок, можно не обращать внимания -->
      <!-- <div>
        <v-chip>
          <toolbarInfo
            title="img by raw src"
            srcUrl="./images/cameraTips/Горизонтальный_уол_камеры.png"
          />
        </v-chip>
        <v-divider class="my-2"></v-divider>
        <v-chip>
          <toolbarInfo
            title="img by role & name"
            desc="здесь я могу написать любую подсказу"
            imgRole="cameraTips"
            tipUrl="Горизонтальный_уол_камеры.png"
          />
        </v-chip>
      </div>  -->
      
      
      
      <!-- выбор марки кабеля -->
      <v-dialog
          v-model="chooseCable"
          transition="dialog-bottom-transition"
          width="80%"
          :scrollable="false"
          aria-hidden="true"
        >
        <template v-slot:activator="{ props2 }">
            
          <v-row>
            <v-col>
              <p class="mt-5">выбранный кабель:</p>
              <p class="mt-n5">{{ chosenCable['Mark'] }} ( MDRN: {{ chosenCable['MDRN'] }} )</p>
              <!-- здесь можно сделать сам вывод выбранных значений -->
            </v-col>
            <v-col>
              <v-btn
                class="mt-5"
                width="100%"
                color="primary"
                v-bind="props2"
                @click="chooseCable = true"
                ><v-icon>mdi-cable-data</v-icon>
                выбрать кабель
              </v-btn>
            </v-col>
          </v-row>
        </template>

        <tableSpace
          @anotherGetVlsSelected="cableWriteSelected"
        />    

      </v-dialog>
      

      <!-- расстояние вежду опорами -->
      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable 
        
        :rules="rule"
        hide-details="auto"
        v-model.number="endpointDistance"
        class="mt-5"
      >
        <template v-slot:label>
            <toolbarInfo
                title="расстояние вежду опорами (м)"
                imageUrl='strelaProvesa'
                desc = "
                Расстояние между соседними опорами, в метрах. Рекомендуется не брать меньше 1м
                "
            />
        </template>                
      </v-text-field>
      
      <!-- высота установки кабеля -->
      <v-text-field
        class="mt-5"
        flat
        type="number"
        required
        outlined
        clearable 
        :rules="rule"
        hide-details="auto"
        v-model.number="height"
      >
        <template v-slot:label>
            <toolbarInfo
                title="высота установки кабеля (м)"
                desc = "
                Высота установки кабеля от земли, в метрах. От этого будут зависить нагрузки,
                связанные с погодными условиями (гололёд, ветровая нагрузка и другие).
                Рекомендуется не брать сильно больше 70 метров, так как там значения будут уже менее точными.
                "
            />
        </template>                
      </v-text-field> 

      <!-- Стрела провеса -->
      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable 
        
        :rules="rule"
        hide-details="auto"
        v-model.number="strelaProvesaInput"
        class="mt-5"
      >
        <template v-slot:label>
            <toolbarInfo
                title="Стрела провеса, м"
                imageUrl='strelaProvesa'
                desc = "Стрела провеса, в метрах. Мера того, насколько прогибается 
                кабель в своей нижайшей точке. В реальности берутся относительно небольшие 
                значения, но и меньше единицы лучше не ставить"
            />
        </template>                
      </v-text-field>

      <!-- перепад высот соседних опор -->
      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable 
        
        hide-details="auto"
        v-model.number="h"
        class="mt-5"
      >
        <template v-slot:label>
            <toolbarInfo
                title="перепад высот соседних опор, м"
                imageUrl='perepadVisot'
                v-bind:desc = "componentInfo.incomes[3]"
            />
        </template>                
      </v-text-field>

      <!-- средние темпрературы -->
      <v-row class="mt-2">
        <v-col>
          <v-text-field
            flat
            type="number"
            required
            outlined
            clearable 
            
            :rules="rule"
            hide-details="auto"
            v-model.number="T_sr"
            label="температура эксплуатации (С*)"
          >
            <template v-slot:label>
                <toolbarInfo
                    title="температура эксплуатации (С*)"
                    v-bind:desc = "componentInfo.incomes[4]"
                />
            </template>                
          </v-text-field>
        </v-col>
        
        <v-col>
          <v-text-field
            flat
            type="number"
            required
            outlined
            clearable 
            
            :rules="rule"
            v-model.number="T"
          >
            <template v-slot:label>
                <toolbarInfo
                    title="температура кабеля в условиях эксплуатации (С*)"
                    v-bind:desc = "componentInfo.incomes[5]"
                />
            </template>                
          </v-text-field>  
        </v-col>       
      </v-row>

      <!-- мин/макс температуры -->
      <v-row class="mt-2">
        <v-col>
          <v-text-field
            flat
            type="number"
            required
            outlined
            clearable 
            :rules="rule"
            hide-details="auto"
            v-model.number="T_min"
          >
            <template v-slot:label>
                <toolbarInfo
                    title="мин. температура эксплуатации (С*)"
                    desc = "минимальная темпиратура эксплуатации, в градусах. можно вводить 
                    любые значения, но меньше -273 градусов не будет иметь смысла"
                />
            </template>                
          </v-text-field>
        </v-col>
        
        <v-col>
          <v-text-field
            flat
            type="number"
            required
            outlined
            clearable 
            :rules="rule"
            hide-details="auto"
            v-model.number="T_max"
          >
            <template v-slot:label>
                <toolbarInfo
                    title="макс температура эксплуатации (С*)"
                    desc = "максимальная темпиратура эксплуатации, в градусах. можно вводить 
                    любые значения."
                />
            </template>                
          </v-text-field>  
        </v-col>
      </v-row>


      <v-row >
        <!-- выбор районов гололеда -->
        <v-col>
          <v-dialog
              v-model="chooseZoneIce"
              transition="dialog-bottom-transition"
              width="80%"
              :scrollable="false"
              aria-hidden="true"
            >
            <template v-slot:activator="{ props3 }">
              <v-btn
                class="mt-2"
                width="100%"
                color="primary"
                v-bind="props3"
                @click="chooseZoneIce = true"
                ><v-icon>mdi-cable-data</v-icon>
                выбрать зону по гололеду
              </v-btn>

              <p class="mt-2">выбранная зона по гололеду: {{ iceField }}</p>
              <!-- здесь можно сделать сам вывод выбранных значений -->
            </template>

            <IceZoneTable
                  @getIceZoneSelected="iceWriteSelected"
                />

          </v-dialog>
        </v-col>
        <!-- выбор районов ветра -->
        <v-col>
          <v-dialog
            v-model="chooseZoneWind"
            transition="dialog-bottom-transition"
            width="80%"
            :scrollable="false"
            aria-hidden="true"
          >
            <template v-slot:activator="{ props3 }">
              <v-btn
                class="mt-2"
                width="100%"
                color="primary"
                v-bind="props3"
                @click="chooseZoneWind = true"
                ><v-icon>mdi-cable-data</v-icon>
                выбрать зону по ветру
              </v-btn>

              <p class="mt-2">выбранная зона по ветру: {{ windField }}</p>
              <!-- здесь можно сделать сам вывод выбранных значений -->
              
            </template>

            <AirZoneTable
                  @getAirZoneSelected="windWriteSelected"
                />

          </v-dialog>
        </v-col>
      </v-row>

      <!-- кнопки -->
      <v-row class="mt-2">
        <v-col
          cols="100%"
        >
          <v-btn 
            @click="started_manual" 
            color="primary" 
            width="100%"
          >Старт</v-btn>
        </v-col>
        <v-col
          cols="auto"
        >
          <v-menu 
            top 
            offset-y 
            :close-on-content-click="false" 
            v-model="menu"
          >
          
            <template v-slot:activator="{ on, attrs }">
              <v-icon
                class="mt-2"
                v-bind="attrs"
                v-on="on"
              >mdi-cog
              </v-icon>
            </template>
            <v-card>
              <v-list>
                  <v-list-item>
                    <v-list-item-content>
                      <v-list-item-title>Число знаков после запятой</v-list-item-title>
                      <v-list-item-subtitle>Определяет точность расчетов и вид выходных значений</v-list-item-subtitle>
                    </v-list-item-content>
                    <v-list-item-action>
                      <v-text-field
                        outlined
                        clearable 
                        label="число знаков после запятой"
                        v-model.number="decimalsRounding"
                        type="number"
                      />
                    </v-list-item-action>
                  </v-list-item>
                <v-btn
                  color="primary"
                  text
                  @click="menu = false"
                >
                  закрыть
                </v-btn>
              </v-list>
            </v-card>
          </v-menu>
        </v-col>
      </v-row>
<!-- 
      </v-card> -->
      <div v-show="started">
      <br>
      <v-divider/>
      <v-row>
          <v-col  cols="100%">
          <p class="text-left pt-3">
              <v-icon :color="rulesViolation ? 'red' : 'green'">
                {{rulesViolationIcon}}
              </v-icon>
              {{ rulesViolationText }} 
          </p>
          </v-col>
          <v-col cols="1">
              <v-btn
                class="mt-3"
                v-if="started"
                icon
                @click="snackbar=true; errorText = 'Функция ещё не реализована '"
              >  
                <v-icon>
                  mdi-share
                </v-icon>
              </v-btn>
            </v-col>
            <!-- <v-col cols="1">
              <v-btn
                class="mt-3"
                v-if="started"
                icon
                @click="started = false"
              >
                <v-icon>
                  mdi-close
                </v-icon>
              </v-btn>
            </v-col> -->
            <v-col cols="2">  
              <v-btn
                class="mt-3"
                v-if="started"
                icon
                @click="started = false"
              >
                <v-icon>
                  mdi-eye-off
                </v-icon>
              </v-btn>
          </v-col>
      </v-row>
      

      <v-divider/>
        
      <!-- <h2>Результаты расчётов воздушных линий связи:</h2> -->
        <v-simple-table>
        <template v-slot:default>
          <thead>
            <tr>
              <th class="text-left">
                Параметр
              </th>
              <th class="text-left">
                Значение:
              </th>
              <!-- <th class="text-left" v-if="rulesViolation">
                Примечания:
              </th> -->
              </tr>
          </thead>
          <tbody>
            <!-- 2.1 -->
            <tr>
              <td>Вес кабеля </td>
              <td> 
                <v-chip
                  :color="getColorBinary(violation_2_1)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(W_kab) + '  Н/м'"
                      :desc = "componentInfo.outcomes[0] + `. Значение ${(violation_2_1 ? ' не' : '')} находится в допустимых пределах` + (violation_2_1 ? ' что-то пошло не так. Проверьте введенные данные.' : '')"
                />
                </v-chip>
              </td>
            </tr>

            <!-- 2.2 -->
            <tr>
              <td>Растягивающая нагрузка </td>
              <td> 
                <v-chip
                  :color="getColorBinary(violation_2_2)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(H_nach) + '  Н'"
                      :desc = "componentInfo.outcomes[1] + `. Значение ${(violation_2_2 ? ' не' : '')} находится в допустимых пределах`+ (violation_2_2 ? ' что-то пошло не так. Проверьте введенные данные.' : '')"
                /></v-chip>
                
              </td>
            </tr>

            <!-- 2.3 -->
            <tr>
              <td>Малый эквивалентный пролет </td>
              <td>
                <v-chip
                  :color="getColorBinary(violation_2_3)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(endpointDistance1) + '  м'"
                      :desc = "componentInfo.outcomes[2] + `. Значение ${(violation_2_3 ? ' не' : '')} находится в допустимых пределах`+ (violation_2_3 ? ' что-то пошло не так. Проверьте введенные данные.' : '')"
                /></v-chip>
              </td>
            </tr>
            <tr>
              <td>Больший эквивалентный пролет </td>
              <td> <v-chip
                  :color="getColorBinary(violation_2_3)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(endpointDistance2) + '  м'"
                      :desc = "componentInfo.outcomes[3] + `. Значение ${(violation_2_3 ? ' не' : '')} находится в допустимых пределах`"
                /></v-chip></td>
              <td v-if="violation_2_3"> что-то пошло не так </td>
            </tr>
            <tr>
              <td>Малая стрела провеса </td>
              <td><v-chip
                  :color="getColorBinary(violation_2_3)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(strelaProvesa1) + '  м'"
                      :desc = "componentInfo.outcomes[4] + `. Значение ${(violation_2_3 ? ' не' : '')} находится в допустимых пределах`"
                /></v-chip></td>
              <td v-if="violation_2_3"> что-то пошло не так </td>
            </tr>
            <tr>
              <td>Большая стрела провеса </td>
              <td><v-chip
                  :color="getColorBinary(violation_2_3)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(strelaProvesa2) + '  м'"
                      :desc = "componentInfo.outcomes[5] + `. Значение ${(violation_2_3 ? ' не' : '')} находится в допустимых пределах`"
                /></v-chip></td>
              <td v-if="violation_2_3"> что-то пошло не так, ваша высота кабеля равна {{ height }} </td>
            </tr>

            <!-- 2.4 -->
            <tr>
              <td>Длина подвешенного кабеля </td>
              <td><v-chip
                  :color="getColorBinary(violation_2_4)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(cableLength) + '  м'"
                      :desc = "componentInfo.outcomes[6] + `. Значение ${(violation_2_4 ? ' не' : '')} находится в допустимых пределах`"
                /></v-chip></td>
              <td v-if="violation_2_4"> кабель не может быть короче расстояния между опорами </td>
            </tr>

            <!-- 2.5 -->
            <tr>
              <td>Длина кабеля в ненагруженном состоянии </td>
              <td><v-chip
                  :color="getColorBinary(violation_2_5)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(cableLengthNoLoad) + '  м'"
                      :desc = "componentInfo.outcomes[7] + `. Значение ${(violation_2_5 ? ' не' : '')} находится в допустимых пределах`+ (violation_2_5 ? ' Кабель не может быть короче расстояния между опорами. Для исправления рекомендуется увеличить стрелу провеса.' : '')"
                /></v-chip></td>
              <!-- <td v-if="violation_2_5"> кабель не может быть короче расстояния между опорами </td> -->
            </tr>

            <!-- 2.6 -->
            <tr>
              <td>Длина кабеля в ненагруженном состоянии с учетом температуры </td>
              <td><v-chip
                  :color="getColorBinary(violation_2_6)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(cableLengthAtMaxTemperature) + '  м'"
                      :desc = "componentInfo.outcomes[8] + `. Значение ${(violation_2_6 ? ' не' : '')} находится в допустимых пределах`+ (violation_2_6 ? ' Кабель не может быть короче расстояния между опорами. Для исправления рекомендуется изменить температуры или увеличить стрелу провеса.' : '')"
                /></v-chip></td>
              <!-- <td v-if="violation_2_6"> кабель не может быть короче расстояния между опорами </td> -->
            </tr>

            <!-- 2.7 -->
            <tr>
              <td>Вес кабеля при воздействии максимального гололеда </td>
              <td> 
                <v-chip
                  :color="getColor(cableWeightMaxIce, 20, 50)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(cableWeightMaxIce) + '  Н/м'"
                      :desc = "componentInfo.outcomes[9] 
                      + '.' + getWarning(getColor(cableWeightMaxIce, 20, 50))
                      + (violation_2_7 ? ' Вес кабеля огромен, более 50 Н/м. Для исправления рекомендуется смягчить климатические нагрузки, сменить марку кабеля.' : '')
                      "
                /></v-chip>
              </td>
              <!-- <td v-if="violation_2_7"> вес кабеля огромен, более 50 Н/м </td> -->
            </tr>

            <!-- 2.8 -->
            <!-- ice -->
            <tr>
              <td>Ветровая нагрузка на кабель в режиме максимального гололеда </td>
              <td> 
                <v-chip
                  :color="getColor(cableLoadMaxIce, 10, 30)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(cableLoadMaxIce) + '  Н/м'"
                      :desc = "componentInfo.outcomes[10] 
                        + '.' + getWarning(getColor(cableLoadMaxIce, 10, 30))
                        + (violation_2_8_ice ? ' Нагрузка огромна, больше 30 Н/м. Для исправления рекомендуется смягчить климатические нагрузки, уменьшить высоту установки или расстояние между опорами.' : '')
                      "
                /> 
                </v-chip>
              </td>
              <!-- <td v-if="violation_2_8_ice"> нагрузка огромна, больше 30 Н </td> -->
            </tr>
            <!-- wind -->
            <tr>
              <td>Ветровая нагрузка на кабель в режиме максимального ветра </td>
              <td> 
                <v-chip
                  :color="getColor(cableLoadMaxWind, 20, 50)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(cableLoadMaxWind) + '  Н/м'"
                      :desc = "componentInfo.outcomes[11] 
                      + '.' + getWarning(getColor(cableLoadMaxWind, 20, 50))
                      + (violation_2_8_wind ? ' Нагрузка огромна, больше 50 Н/м. Для исправления рекомендуется смягчить климатические нагрузки, уменьшить высоту установки или расстояние между опорами.' : '')
                      "
                />
                </v-chip>
              </td>
              <!-- <td v-if="violation_2_8_wind"> нагрузка огромна, больше 50 Н </td> -->
            </tr>

            <!-- 2.9 -->
            <tr>
              <td>Максимальная нагрузка, действующая на кабель </td>
              <td> 
                <v-chip
                  :color="getColor(W_max, 20, 50)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(W_max) + '  Н/м'"
                      :desc = "componentInfo.outcomes[12] 
                        + '.' + getWarning(getColor(W_max, 20, 50))
                        + (violation_2_9 ? ' Нагрузка огромна, больше 50 Н/м. Для исправления рекомендуется смягчить климатические нагрузки, уменьшить высоту установки или расстояние между опорами.' : '')
                        "
                /></v-chip>
              </td>
            </tr>

            <!-- 2.10 -->
            <tr>
              <td>Максимальная стрела провеса </td>
              <td> 
                <v-chip
                  :color="getColor(strelaProvesa_max, (height/2), height)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(strelaProvesa_max) + '  м'"
                      :desc = "componentInfo.outcomes[13] 
                      + '.' + getWarning(getColor(strelaProvesa_max, (height/2), height))
                      + (violation_2_10 ? ' Кабель будет лежать на земле. Для исправления рекомендуется смягчить условия, увеличить высоту установки или уменьшить расстояние между опорами.  Возможно сменить марку кабеля.' : '')
                      "
                /> 
                </v-chip>
              </td>
            </tr>

            <!-- 2.11 -->
            <tr>
              <td>Максимальная растягивающая нагрузка при наихудших условиях

              </td>
              <td> 
                <v-chip
                  :color="getColor(H_max, 40, 50)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(H_max) + '  Н'"
                      :desc = "componentInfo.outcomes[14] 
                        + '.' + getWarning(getColor(H_max, 40, 50))
                        + (violation_2_11 ? ' Больше предельно допусимых 50 Н. Для исправления рекомендуется смягчить условия, снизить высоту установки или расстояние между опорами.' : '')
                      "
                />
                </v-chip>
              </td>
            </tr>

            <!-- 2.13 -->
            <tr>
              <td>
                Конечная стрела провеса при нормальных условиях 
              </td>
              <td> 
                <v-chip
                  :color="getColor(strelaProvesa_n_vit, (height/2), height)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(strelaProvesa_n_vit) + '  м'"
                      :desc = "
                        componentInfo.outcomes[15] 
                        + '.' + getWarning(getColor(strelaProvesa_n_vit, (height/2), height)) 
                        + (violation_2_13_S ? ' Кабель лежит на земле. Для исправления рекомендуется величить высоту установки или уменьшить стрелу провеса. Также можно изменить климатические условия ' : '')
                        "
                /> 
                </v-chip>
              </td>
            </tr>
            <tr>
              <td>нагрузка после вытяжки при нормальных условиях </td>
              <td> 
                <v-chip
                  :color="getColor(H_n_vit, 30, 50)"
                  dark
                ><toolbarInfo
                      :title="DotToCommas(H_n_vit) + '  Н'"
                      :desc = "
                      componentInfo.outcomes[16] 
                      + '.' + getWarning(getColor(H_n_vit, 30, 50))
                      + (violation_2_13_H ? ' Нагрузка очень высока и превышает 50 Н.' : '')
                      "
                />
                </v-chip>
              </td>
            </tr>

          </tbody>
        </template>
      </v-simple-table>

      <!-- <v-btn
        disabled
        class="mt-2  mr-5"
      >сохранить (WIP)</v-btn> -->
      <!-- <v-btn
        @click="started=false"
        class="mt-2"
      >скрыть</v-btn> -->

    </div>
    </v-card>
    

    <!-- уведомление об ошибке -->
    <v-snackbar
      v-model="snackbar"
      :timeout="timeout"
    >
      {{ errorText }}

      <template v-slot:action="{ attrs }">
        <v-btn
          color="accent"
          text
          v-bind="attrs"
          @click="snackbar = false"
        >
          OK
        </v-btn>
      </template>
    </v-snackbar>

  </div>

</template>

<script>
import headerTab from './ui/commonUi/header.vue';
import tableSpace from './ui/forVls/tableSpace.vue';
import toolbarInfo from './ui/commonUi/tooltip.vue';
import AirZoneTable from './ui/forVls/ClimateTables/AirZoneTable.vue';
import IceZoneTable from './ui/forVls/ClimateTables/IceZoneTable.vue';

export default {
  name: "vls_vue",
  components: {
    headerTab,
    tableSpace,
    toolbarInfo,
    AirZoneTable,
		IceZoneTable
  },
  data() {
    return {
      menu: false,
      started: false,
      snackbar: false,      // окошко об ошибке
      timeout: 2500,        // время высвечивания окна об ошибке
      errorText: 'Неверно введены данные или они отсутствуют',
      debug: false,

      rulesViolation: false,
      rulesViolationIcon: 'mdi-ghost',
      rulesViolationText: 'пока не введено',
      violation_2_1: false,
      violation_2_2: false,
      violation_2_3: false,
      violation_2_4: false,
      violation_2_5: false,
      violation_2_6: false,
      violation_2_7: false,
      violation_2_8_ice: false,
      violation_2_8_wind: false,
      violation_2_9: false,
      violation_2_10: false,
      violation_2_11: false,
      violation_2_12: false,
      violation_2_13_S: false,
      violation_2_13_H: false,
      violation_2_14: false,
      

      selected: '',
      chooseCable: false,
      chosenCable: {
        Mark: "ДПТС до 48ОВ (6x8)",
        MDRN: 4,
        MRN: 1.0,
        MPR: 8.48,
        Weight: 115.6,
        Diameter: 40.2,
        Slice: 116.3,
        L_nach: 4.56,
        L_Kon: 4.92,
        L_feat: 3.19,
        TKLR: 16.92,
      },

      chooseZoneIce: false,
      chooseZoneWind: false,

      componentInfo: 
      {
        name: 'Воздушные линии связи',
        incomes: [
        'm, погонный вес кабеля (кг/км)', 
        'L, расстояние между столбами (м)',
        'strelaProvesaInput, стрела провеса - насколько кабель провесает в нижайшей точке (м)',
        'h – перепад высот между точками подвеса кабеля (м)',
        'Тср – средняя температура эксплуатации (С*)',
        'TкабСр – температура кабеля в условиях эксплуатации (С*)', 
        'height - высота расположения приведенного центра тяжести проводов, троссов и средних точек',
        'T_min - минимальная температура эксплуатации (С*)',
        'T_max - максимальная температура эксплуатации (С*)',
        'зона по гололёду',
        "зона по ветровому давлению",
        "марка кабеля и его характеристики (вес, растягивающие усиления, модули упругости, температурный коэффициент линейного расширения, сечение и диаметр)"
        ],
        outcomes: [
        'Вес кабеля',
        "Растягивающая нагрузка",
        "Малый эквивалентный пролет (при разности высот)",
        "Больший эквивалентный пролет (при разности высот)",
        "Малая стрела провеса (при разности высот)",
        "Большая стрела провеса (при разности высот)",
        "Длина подвешенного кабеля",
        "Длина кабеля в ненагруженном состоянии",
        "Длина кабеля в ненагруженном состоянии с учетом температуры",
        "Вес кабеля при воздействии максимального гололеда",
        "Ветровая нагрузка на кабель в режиме максимального гололеда",
        "Ветровая нагрузка на кабель в режиме максимального ветра",
        "Максимальная нагрузка, действующая на кабель",
        "Максимальная стрела провеса",
        "Максимальная растягивающая нагрузка при наихудших условиях",
        "Конечная стрела провеса при нормальных условиях",
        "Нагрузка после вытяжки при нормальных условиях"
        
        ], 
        description: 'Эталонный сценарий - две опоры с небольшой разницей высот, между которыми протянут кабель воздушных линий связи.',
        disclaimers: [
        'округления идут в большую сторону, предполагается наихудший сценарий'
        ]
      },
      
      rule: [
          value => !!value || 'Необходимо заполнить это поле.',
      ],                              //Правила для текстовых полей
      decimalsRounding: 4, // число знаков после запятой при округлении. Где-то может расширяться или снижаться
      // константы:
      g: 9.8,         // ускорение свободного падения (м/с^2)
      constIceMass: 0.0009,   // объёмная масса гололеда (кг/см^3)
                      // БЫТЬ ОСТОРОЖНЫМ СО СТЕПЕНЬЮ ДЕСЯТКИ И ПЕРЕВОДИТЬ ДО ОТПРАВКИ

      // входные параметры
      endpointDistance: 50,    // расстояние вежду опорами (м)
      strelaProvesaInput: 1,     // Стрела провеса
      h: 1,    // Перепад высот между точками подвеса кабеля
      height:25,// Высота расположения приведенного центра тяжести проводов, троссов и средних точек 

      // не знаю, как правильно это оценивать
      T_sr: 24, // Средняя температура эксплуатации
      T: 20,    // температура кабеля в условиях эксплуатации

      T_min: -50, //минимальная температура
      T_max: 50, //максимальная температура

      
  // потом это будет получаться из табличек о климате, но пока так
      W: 400,     // нормативное ветровое давление, Па
      windField: 1, // район по ветру
      iceField: 1,  // район по гололеду
  // Из таблицы района по ветру         (за предустановленные взял 1 район)
      W_0: 400,   // Нормативно ветровое давление на высоте 10 метров над землёй
      v_0: 25,    // скорость ветра

  // Из таблицы района гололеда         (за предустановленные взял 1 район)
      C: 10,       // толщина стенки гололеда (мм)

  // Получаемое из таблички кабеля
  // для примера берётся ДПТ до 48ОВ, с МДРН = 4
      // ЗДЕСЬ Я ХЗ, КАКАЯ ИМЕННО УПРУГОСТЬ. ЛИБО НАЧАЛЬНАЯ, ЛИБО КОНЕЧНАЯ, ЛИБО ВЫТЯЖКИ... беру начальную
      elasticity_kab: 4.56,        // модуль упругости кабеля (кН/мм^2)
      elasticity_vit: 3.19,
      elasticity_kon: 4.92, 
      Diameter: 0,        // диаметр кабеля
      strelaProvesa_kab: 116.3,       // сечение кабеля (мм^2)
      TKLR: 0.00001692,   // Темпиратурный коэффициент линейного расширения (1/С*)
                          // БЫТЬ ОСТОРОЖНЫМ СО СТЕПЕНЬЮ ДЕСЯТКИ И НЕ ПЕРЕВОДИТЬ ДО ОТПРАВКИ. Принимающая функция переводит из коэффициента 10^(-6)
      cableMass: 115.6,   // удельный весь кабеля (кг/км)
      strelaProvesa_vit: 3.19,
      strelaProvesa_kon: 4.92,



  // Из таблички о высоте расположения  (за предустановленные взял 25м)
  // используется интерполяция для всех трёх
      K_i: 1,     // Коэффициент изменения толщины стенки гололеда по высоте над поверхностью земли
      d: 10,      // диаметр кабеля (мм)
      K_d: 1,     // Коэффициент изменение толщины стенки гололеда в зависимости от диаметра провода (троса)
      heightTable: [
        { height: 25,
          K_i: 1,
          d: 10,
          K_d: 1,
        },
        { height: 30,
          K_i: 1.4,
          d: 20,
          K_d: 0.9,
        },
        { height: 50,
          K_i: 1.6,
          d: 30,
          K_d: 0.8,
        },
      ],







      

      // нужные для внутренних расчетов
      W_kab: 0,   // Вес кабеля (Н/м)
      H_nach: 0,  // Растягивающая нагрузка (Н)
      H_max: 0,   // Максимальная растягивающая нагрузка
      H_max_vit: 0, // Максимальная растягивающая нагрузка после вытяжки
      H_n_vit: 0, // Растягивающая нагрузка вытяжки

      endpointDistance1: 0,      // Малый эквивалентный пролет
      endpointDistance2: 0,      // Больший эквивалентный пролет

      endpointDistance1_max:0,
      endpointDistance2_max: 0,


      // используется для составления монтажной таблицы
      // endpointDistance_n_MON_T: 0,  // длина кабеля в ненагруженном состоянии с учетом монтажной температуры
      // endpointDistance_MON_nk: 0,   // длина кабеля в ненагруженном состоянии при различной температуре
      // T_mon: 0,      // температура кабеля в условиях эксплуатации 

      strelaProvesa1: 0,      //
      strelaProvesa2: 0,      //
      
      strelaProvesa1_max: 0,
      strelaProvesa2_max: 0,

      strelaProvesa1_n_vit: 0,
      strelaProvesa2_n_vit: 0,

      strelaProvesa_n_vit: 0, // Стрела провеса вытяжки

      cableLength: 0,   // Длина подвешенного кабеля
      cableLengthNoLoad: 0,    // Длина кабеля в ненагруженном состоянии
      cableLengthAtMaxTemperature: 0,    // Длина кабеля с учетом температуры
      cableLengthMax: 0,   // Длина кабеля в нагруженном состоянии

      cableWeightMaxIce: 0,     // Вес кабеля при воздействии максимального гололеда
      cableLoadMaxIce: 0, // Нагрузка при максимальном гололеде
      cableLoadMaxWind: 0,// Нагрузка при максимальном ветре
      strelaProvesa_max: 0,


      K_l: 1.2,       // коэффициент учитывающий влияние длины пролета на ветровую нагрузку
      // определяется по табличке, интерполяцией
      K_lTable:[
        {
          height: 50,
          K_l: 1.2
        },
        {
          height: 100,
          K_l: 1.1
        },
        {
          height: 150,
          K_l: 1.05
        },
        {
          height: 250,
          K_l: 1
        }

      ],

      terrainType: 'A', // тип местности, где:
                        /*
                          A - открытые побережья морей, озер, водохранилищ, пустыни, степи, лесостепи, тундра;
                          
                          В — городские территории, лесные массивы и другие местности, равномерно покрытые препятствиями высотой не менее 2/3 высоты опор; 
                          
                          С — городские районы с застройкой зданиями высотой более 25 м, просеки в лесных массивах с высотой деревьев более высоты опор, орографически защищенные извилистые и узкие склоновые долины и ущелья.
                        */
      K_w: 0,
      // для интерполяции в пункте 2.8
      K_wTable:[
        {
          height: 15,
          K_w_A: 1,
          W_w_B: 0.65,
          K_w_C: 0.4,
        },
        {
          height: 20,
          K_w_A: 1.25,
          W_w_B: 0.85,
          K_w_C: 0.55,
        },
        {
          height: 40,
          K_w_A: 1.5,
          W_w_B: 1.1,
          K_w_C: 0.8,
        },
        {
          height: 60,
          K_w_A: 1.7,
          W_w_B: 1.3,
          K_w_C: 1,
        },
        {
          height: 80,
          K_w_A: 1.85,
          W_w_B: 1.45,
          K_w_C: 1.15,
        },
        {
          height: 100,
          K_w_A: 2,
          W_w_B: 1.6,
          K_w_C: 1.25,
        },
        {
          height: 150,
          K_w_A: 2.25,
          W_w_B: 1.9,
          K_w_C: 1.55,
        },
        {
          height: 200,
          K_w_A: 2.45,
          W_w_B: 2.1,
          K_w_C: 1.8,
        },
        {
          height: 250,
          K_w_A: 2.65,
          W_w_B: 2.3,
          K_w_C: 1.8,
        },
        {
          height: 300,
          K_w_A: 2.75,
          W_w_B: 2.5,
          K_w_C: 2.2,
        },
        {
          height: 350,
          K_w_A: 2.75,
          W_w_B: 2.75,
          K_w_C: 2.35,
        },
        
      ],


      a_w: 0,     
      a_wTable: [
        {
          a_w: 1,
          W_0: 200
        },
        {
          a_w: 0.94,
          W_0: 240
        },
        {
          a_w: 0.88,
          W_0: 280
        },
        {
          a_w: 0.85,
          W_0: 300
        },
        {
          a_w: 0.83,
          W_0: 320
        },
        {
          a_w: 0.8,
          W_0: 360
        },
        {
          a_w: 0.76,
          W_0: 400
        },
        {
          a_w: 0.71,
          W_0: 500
        },
        {
          a_w: 0.7,
          W_0: 580
        },
      ],

      C_x: 0,           // коэффициент лобового сопротивления
      // если диаметр менее 20мм, то равен 1.2. Иначе - 1.1

      W_max: 0,         // Максимальная нагрузка, действующая на кабель


      cableLengthNoLoad_minT: 0,      // длина кабеля в ненагруженом состоянии при минимальной темпиратуре
      cableLengthNoLoad_maxT: 0,      // длина кабеля в ненагруженом состоянии при максимальной темпиратуре
      cableLength_vit: 0,     // длина кабеля после вытяжки
      cableLength_vit_0: 0,

    };
  },
  methods: {

    // функция возвращающая цвет
    // example_limit_1 - уровень, выше которого желтый
    //  example_limit_2 - уровень, выше которого красный
    getColor(data, example_limit_1, example_limit_2){
      if (data <= example_limit_1) 
      { 
        if (data <= 0) 
        {
          return 'red'
        } 
        return 'green'
      } 
      else if (data <= example_limit_2) 
      {
        return 'orange'
      } 
      return 'red'
    },

    // функция возвращающая цвет, но только красный при ошибке и серый при нормальной работе
    getColorBinary( violation ){
      if (violation) return 'red'
      return 'grey'
    },

    // функция на основании вывода getColor() выдаёт предупреждение о близости предела
    getWarning(color){
      if (color == 'red') return ' Значение не находится в допустимых пределах.'
      else if (color == 'orange') return ' Значение находится в допустимых пределах, но очень близко к выходу за них.'
      else if (color == 'green') return ' Значение находится в допустимых пределах.'
      else if (color == 'grey') return ' Параметр не имеет как таковых строго определенных пределов, но оно имеет физический смысл.'
      return 'ну здесь я не придумал, что написать, либо цвет не тот'
    },

    // функция говорит, если введенные значения неправильны
    inputViolation(){
      return (
        this.L <1
        || this.strelaProvesaInput<1
        || this.h<0
        || this.height<1
        || (this.T_min>this.T_max)
      )
    },
    getRulesViolationIcon(){
      if (this.rulesViolation) this.rulesViolationIcon = "mdi-alert"
      else this.rulesViolationIcon = "mdi-check"
    },
    getRulesViolationText(){
      if (this.rulesViolation) this.rulesViolationText = "Расчет содержит недопустимые значения, рекомендуется пересмотреть введенные требования"
      else this.rulesViolationText = "Расчет не содержит недопустимых значений"
    },

    // для таблицы ответа
    getRulesViolation () {
        // 2.1
        this.getViolation_2_1()
        // 2.2
        this.getViolation_2_2()
        // 2.3
         this.getViolation_2_3()
        // 2.4
         this.getViolation_2_4()
        // 2.5
         this.getViolation_2_5()
        // 2.6
         this.getViolation_2_6()
        // 2.7
         this.getViolation_2_7()
        // 2.8
         this.getViolation_2_8_ice()
         this.getViolation_2_8_wind()
        // 2.9
         this.getViolation_2_9()
        // 2.10
         this.getViolation_2_10()
        // 2.11
         this.getViolation_2_11()
        // 2.13
         this.getViolation_2_13_S()
         this.getViolation_2_13_H()
      
      this.rulesViolation =(
        // 2.1
        this.violation_2_1
        // 2.2
        || this.violation_2_2
        // 2.3
        || this.violation_2_3
        // 2.4
        || this.violation_2_4
        // 2.5
        || this.violation_2_5
        // 2.6
        || this.violation_2_6
        // 2.7
        || this.violation_2_7
        // 2.8
        || this.violation_2_8_ice
        || this.violation_2_8_wind
        // 2.9
        || this.violation_2_9
        // 2.10
        || this.violation_2_10
        // 2.11
        || this.violation_2_11
        // 2.13
        || this.violation_2_13_S
        || this.violation_2_13_H
      )
    },

    getViolation_2_1 () {
      this.violation_2_1 = (this.W_kab<=0 && !! this.W_kab )
    },
    getViolation_2_2 () {
      this.violation_2_2 =  ((this.H_nach<=0 && !!this.H_nach) || (this.H_nach.toString() == 'Infinity'))
    },
    getViolation_2_3 () {
      this.violation_2_3 =  (
        (this.endpointDistance1>this.endpointDistance2) 
        || (this.endpointDistance2<this.endpointDistance1) 
        || (this.strelaProvesa1<=0) 
        || (this.strelaProvesa2<=0 && this.strelaProvesa2>this.height) 
        || (
          (this.endpointDistance1.toString() == 'Infinity')  
          || (this.endpointDistance1.toString() == '-Infinity')
        )
      )
    },
    getViolation_2_4 () {
      this.violation_2_4 =  (this.cableLength<this.endpointDistance)
    },
    getViolation_2_5 () {
      this.violation_2_5 =  (this.cableLengthNoLoad<this.endpointDistance)
    },
    getViolation_2_6 () {
      this.violation_2_6 =  (this.cableLengthAtMaxTemperature<this.endpointDistance)
    },
    getViolation_2_7 () {
      this.violation_2_7 =  (this.getColor(this.cableWeightMaxIce, 20, 50) == 'red')
    },
    getViolation_2_8_ice () {
      this.violation_2_8_ice =  (this.getColor(this.cableLoadMaxIce, 10, 30) == 'red')
    },
    getViolation_2_8_wind () {
      this.violation_2_8_wind =  (this.getColor(this.cableLoadMaxWind, 20, 50)=='red')|| this.cableLoadMaxWind<=0
    },
    getViolation_2_9 () {
      this.violation_2_9 =  (this.getColor(this.W_max, 20, 50) == 'red') || this.W_max<=0
    },
    getViolation_2_10 () {
      this.violation_2_10 =  (this.getColor(this.strelaProvesa_max, (this.height/2), this.height) == 'red') || this.strelaProvesa_max<=0
    },
    getViolation_2_11 () {
      this.violation_2_11 =  (this.getColor(this.H_max, 40, 50) == 'red') || this.H_max<=0
    },
    getViolation_2_13_S () {
      this.violation_2_13_S =  (this.getColor(this.strelaProvesa_n_vit, (this.height/2), this.height) == 'red') || this.strelaProvesa_n_vit<=0
    },
    getViolation_2_13_H () {
      this.violation_2_13_H =  (this.getColor(this.H_n_vit, 30, 50) == 'red')
    },

// // Из таблицы района по ветру         (за предустановленные взял 1 район)
//     W: 0,     // нормативное ветровое давление, Па
//     windField: 1, // район по ветру
//     W_0: 400,   // Нормативно ветровое давление на высоте 10 метров над землёй
//     v_0: 25,    // скорость ветра
    windWriteSelected(data){
      console.log('wind data', `${JSON.stringify(data[0])}`)
      // потом это будет получаться из табличек о климате, но пока так
      this.W = data[0].Norma * 1000     // нормативное ветровое давление, Па
      this.windField = data[0].airZone
      this.W_0 = data[0].windPressure
      this.v_0 = data[0].windSpeed
      // закрыть окно
      this.chooseZoneWind = false
      
    },


// Из таблицы района гололеда         (за предустановленные взял 1 район)
//    C: 10,       // толщина стенки гололеда (мм)
//    iceField: 1,  // район по гололеду
    iceWriteSelected(data){
      console.log('ice data', `${JSON.stringify(data[0])}`)
        // потом это будет получаться из табличек о климате, но пока так
        this.C = data[0].iceWidth     // нормативное ветровое давление, Па
        this.iceField = data[0].Icearea
        // закрыть окно
        this.chooseZoneIce = false
        
    },
    
    cableWriteSelected(data){
      this.chosenCable = data[0]
      console.log('Final get data', this.chosenCable)
      this.chooseCable = false
      this.setCableParams(this.chosenCable)
      
    },

    setCableParams(chosenCable){
      this.elasticity_kab = chosenCable.L_nach    // модуль упругости кабеля (кН/мм^2)
      // ЗДЕСЬ Я ХЗ, КАКАЯ ИМЕННО УПРУГОСТЬ. ЛИБО НАЧАЛЬНАЯ, ЛИБО КОНЕЧНАЯ, ЛИБО ВЫТЯЖКИ... беру начальную
      this.elasticity_vit = chosenCable.L_feat
      this.elasticity_kon = chosenCable.L_kon
      this.strelaProvesa_kab=chosenCable.Slice
      this.Diameter = chosenCable.Diameter
      this.TKLR = chosenCable.TKLR * 0.000001
      this.cableMass = chosenCable.Weight
      
    },

    DotToCommas(data){
      return data.toString().replace(".",",")
    },

    /**
     * Функция интерполяции, универсальная
     * @param {number} value           - как координата X, по которой ищем 
     * @param {massive} dataTable - база данных, где ищем. Является массивом объектов
     * @param {string} searchableValue - искомый параметр объекта массива
     * @param {string} SearchThrough   - какой параметр является координатой X
     */
    interpolateUniversal(value, dataTable, searchableValue, SearchThrough){
      // минимальное значение 
      var minValueX = dataTable[0][SearchThrough]
        var minValueSearched = dataTable[0][searchableValue]  

      // максимальное значение 
      var maxValueX = dataTable[
        dataTable.length - 1
      ][SearchThrough]
        var maxValueSearched = dataTable[
          dataTable.length - 1
        ][searchableValue] 

      if(this.debug){
        console.log(`минимальное значение ${SearchThrough} `, minValueX)
        console.log(`минимальное значение ${searchableValue} `, minValueSearched)

        console.log(`максимальное значение ${SearchThrough} `, maxValueX)
        console.log(`максимальное значение ${searchableValue} `, maxValueSearched)
        
        console.log('введенное значение', value)
      }
      

      // ниже минимума
      if (value <= minValueX){
        if(this.debug) console.warn(`
        при расчете ${searchableValue} по ${SearchThrough}=${value},
        введено меньше минимального, ${value}, '<', ${minValueX}
        `
        )
        if(this.debug) console.log('ответ:', minValueSearched)
        return parseFloat(minValueSearched)
      }
      // превышение максимума
      if (value >= maxValueX){
        if(this.debug) console.warn(`
        при расчете ${searchableValue} по ${SearchThrough}=${value},
        введено больше максимального, ${value}, '>', ${maxValueX}
        `
        )
        if(this.debug) console.log('ответ:', maxValueSearched)
        return parseFloat(maxValueSearched)
      }

      // вычисления с учетом длины массива
      if(this.debug){
        console.log(`предцикловый вывод, входное значение ${value}, проверить минимальное значение ${dataTable[0][SearchThrough]}, длина массива = ${dataTable.length}`)
      }
      for (var i = 0; i<dataTable.length; i++){
        if(this.debug){
          console.log(`итерация ${i}, значение ${value}, сравниваем с ${dataTable[i][SearchThrough]}`)
        }

        if (value < dataTable[i][SearchThrough]){
          var x1 = dataTable[i-1][SearchThrough]
          var x2 = dataTable[i][SearchThrough]

          var y1 = dataTable[i-1][searchableValue]
          var y2 = dataTable[i][searchableValue]

          var result = this.interpolateFormula(value, x1, x2, y1, y2)
          if(this.debug) console.log( searchableValue, '=', result,' как результат интерполяции (',value, x1, x2, y1, y2,')')
          return parseFloat(result)
        }
      }
    },

    /** 
     * формула интерполяции, вынесенная отдельной функцией для удобства. 
     * Строго привязана к функций interpolateUniversal
     * @param {number} value  координата по оси Х, по которой нужно найти Y
     * @param {number} x1     значение по оси Х меньше искомой
     * @param {number} x2     значение по оси Х больше искомой
     * @param {number} y1     значение по оси Y меньше искомой
     * @param {number} y2     значение по оси Y больше искомой
     */
    interpolateFormula(value, x1, x2, y1, y2){
      return (y1 + (value-x1)* ((y2-y1)/(x2-x1))).toFixed(this.decimalsRounding+1)
    },

    // то, что происходит по нажатию кнопки
    started_manual(){
      // проверка введенных данных 
      if (this.inputViolation()){
        this.snackbar=true
        this.errorText = 'Неверно введены данные. '
        return
      }
      // показ окна вывода 
      this.started = true
      // основной расчет
      this.start()
      // проверка полученных данных 
      this.getRulesViolation()
      this.getRulesViolationText()
      this.getRulesViolationIcon()
    },

    // сам скрипт
    start() {
      // как работает этот раздел... хех...
      // Если бы я ещё знал, как он работает.
      // В документе есть много пунктов, я их стал делать по порядку
      if(this.endpointDistance < 1){
        this.endpointDistance = 1
      }

      this.task_2_1()
      this.task_2_2()
      this.task_2_3()
      this.task_2_4()
      this.task_2_5()
      this.task_2_6()
      this.task_2_7()
      this.task_2_8()
      this.task_2_9()
      this.task_2_10()
      this.task_2_11()
      this.task_2_13()
    },
    
    // Вес кабеля
    task_2_1(){
      console.log('cablemass', this.cableMass)
      this.W_kab = parseFloat(
        this.cableMass * this.g / 1000
        ).toFixed(this.decimalsRounding)
      console.log('2.1 вес кабеля, Н/м', this.W_kab)
    },

    // Растягивающая нагрузка, действующая на кабель
    task_2_2(){

      this.H_nach = ((this.W_kab * this.endpointDistance^2) / ( 8 * this.strelaProvesaInput )).toFixed(this.decimalsRounding+2)
      console.log('2.2 Растягивающая нагрузка, Н', this.H_nach)
    },

    // Перепад высот между опорами
    task_2_3(){
      this.endpointDistance1 = (
        this.endpointDistance - (2 * this.h * this.H_nach)/(this.W_kab * this.endpointDistance)
      ).toFixed(this.decimalsRounding+2)
      console.log('2.3 Малый эквивалентный пролет', this.endpointDistance1)

      this.endpointDistance2 = (
        this.endpointDistance + (2 * this.h * this.H_nach)/(this.W_kab * this.endpointDistance)
      ).toFixed(this.decimalsRounding+2)
      console.log('2.3 Больший эквивалентный пролет', this.endpointDistance2)

      // this.strelaProvesa1 = (
      //   (this.W_kab * this.endpointDistance1^2)/(8 * this.H_nach)
      // ).toFixed(this.decimalsRounding+2)
      this.strelaProvesa1 = this.get_S1_or_S2(this.strelaProvesaInput, this.endpointDistance1, this.endpointDistance)
      console.log('2.3 Малая стрела провеса', this.strelaProvesa1)

      // this.strelaProvesa2 = (
      //   (this.W_kab * this.endpointDistance2^2)/(8 * this.H_nach)
      // ).toFixed(this.decimalsRounding+2)
      this.strelaProvesa2 = this.get_S1_or_S2(this.strelaProvesaInput, this.endpointDistance2, this.endpointDistance)
      console.log('2.3 Большая стрела провеса', this.strelaProvesa2)
    },

    // Длина подвешенного кабеля
    task_2_4(){
      this.cableLength = (
        this.endpointDistance + 4/3 * ( (this.strelaProvesa1^2 / this.endpointDistance1) + (this.strelaProvesa2^2 / this.endpointDistance2) )
      ).toFixed(this.decimalsRounding)
      console.log('2.4 длина подвешенного кабеля, м', this.cableLength)
    },

    // Длина кабеля в ненагруженном состоянии
    task_2_5(){
      this.cableLengthNoLoad = (
        (100 * this.cableLength) / (100 * (1 + (this.H_nach / (this.elasticity_kab * this.strelaProvesa_kab))))
      ).toFixed(this.decimalsRounding)
      console.log('2.5 Длина кабеля в ненагруженном состоянии, м', this.cableLengthNoLoad)
    },

    // Длина кабеля в ненагруженном состоянии с учетом температуры
    task_2_6(){
      this.cableLengthAtMaxTemperature = parseFloat(
        this.cableLengthNoLoad * (1 + this.TKLR * (this.T - this.T_sr))
      ).toFixed(this.decimalsRounding)
      // console.log('2.6 ТКЛР', this.TKLR)
      // console.log('2.6 проверка', 1 + this.TKLR * (this.T - this.T_sr))
      console.log('2.6 Длина кабеля в ненагруженном состоянии с учетом температуры, м', this.cableLengthAtMaxTemperature)
    },

    // здесь будет уже упоминание таблицы
    // Вес кабеля при воздействии максимального гололеда
    task_2_7(){
      // для получения нужных Ki, Kd и d
      this.height-=this.strelaProvesaInput
      // this.interpolateHeight(this.height)
      this.K_i = this.interpolateUniversal(
          this.height, 
          this.heightTable, 
          'K_i', 
          'height'
        )
      this.K_d = this.interpolateUniversal(
          this.height, 
          this.heightTable, 
          'K_d', 
          'height'
        )
      this.d = this.interpolateUniversal(
          this.height, 
          this.heightTable, 
          'd', 
          'height'
        )
      // this.interpolateUniversal(this.height, this.heightTable, 'K_i', 'height')

      this.cableWeightMaxIce = parseFloat(
        parseFloat(this.W_kab) + 
        parseFloat(this.constIceMass * this.g * Math.PI * this.K_i * this.K_d * this.C * (this.d + this.C))
        ).toFixed(this.decimalsRounding)
      console.log('2.7 проверка', parseFloat(this.W_kab))
      console.log('2.7 проверка', this.constIceMass)
      console.log('2.7 проверка', this.g)
      console.log('2.7 проверка', Math.PI)
      console.log('2.7 проверка', this.K_i)
      console.log('2.7 проверка', this.K_d)
      console.log('2.7 проверка C', this.C)
      console.log('2.7 проверка d', this.d)
      console.log('2.7 проверка C+d', parseFloat(this.d + this.C))
      console.log('2.7 проверка', parseFloat(this.constIceMass * this.g * Math.PI * this.K_i * this.K_d * this.C * (this.d + this.C)).toFixed(this.decimalsRounding))
      
      console.log('2.7 Вес кабеля при воздействии максимального гололеда, кг', this.cableWeightMaxIce)
      this.height+=this.strelaProvesaInput
    },

    // Ветровая нагрузка на кабель при гололеде
    task_2_8(){
      console.log("2.8 ветровая нагрузка на кабель при гололеде. Здесь бы нам уже получить W_0")
      // по своей таблице от высоты
      this.K_l = this.interpolateUniversal(
          this.height, 
          this.K_lTable, 
          'K_l', 
          'height'
        )
      // по своей таблице от высоты
      this.K_w = this.interpolateUniversal(
        this.height, 
        this.K_wTable, 
        `K_w_${this.terrainType}`, 
        'height'
      )

      // a_w - по табличке ветрового давления
      this.a_w = this.interpolateUniversal(
        this.W_0, 
        this.a_wTable, 
        `a_w`, 
        'W_0'
      )

      // C_x - по табличке от диаметра кабел
      if(this.d >= 20){
        this.C_x = 1.1
      } else { this.C_x = 1.2}

      if(this.debug){
        console.log('Diameter =', this.d)
        console.log('C_x =', this.C_x)
      }
      

      /*W - Нормативное ветровое давление по району

        При расчете в режиме максимального ветра - по табличке (W_0)
        При расчете в режиме максимального гололеда - по формуле 
                    со скоростью ветра при гололеде v_г

                    W = v^2 / 1.6

                    если v_г неизвестен, то
                    W_г = 0.25 * W_0
      */      
      this.cableLoadMaxWind = parseFloat(this.windPressure(this.W_0))
      console.log('ответ для ветра:', this.cableLoadMaxWind)

      if(this.debug) console.log('режим максимального гололеда, таблицы нет, потому W_г = 0.25*W_0 =', this.W_0/4)
      this.cableLoadMaxIce = parseFloat(this.windPressure(this.W_0/4))
      console.log('ответ для гололеда:', this.cableLoadMaxIce)
    },

    // Максимальная нагрузка, действующая на кабель
    task_2_9(){
      this.W_max = parseFloat(
        Math.sqrt(
          (this.cableLoadMaxWind*this.cableLoadMaxWind) + (this.cableLoadMaxIce*this.cableLoadMaxIce)
        ).toFixed(this.decimalsRounding)
      )
      console.warn('2.9 Максимальная нагрузка, действующая на кабель ???', this.W_max)
    },

    // Расчет максимальной стрела провеса
    task_2_10(){
      
      // длина кабеля в нагруженом состоянии
      this.cableLengthMax = this.endpointDistance + (4/3)*((this.strelaProvesa1^2/this.endpointDistance1)+(this.strelaProvesa2^2/this.endpointDistance2))

      // переопределение strelaProvesa1 и strelaProvesa2
      console.log('переопределение strelaProvesa1 и strelaProvesa2')
      console.log(`до:    strelaProvesa1=${this.strelaProvesa1}, strelaProvesa2=${this.strelaProvesa2}`)

      this.strelaProvesa1 = parseFloat(
        this.strelaProvesaInput*this.endpointDistance1*this.endpointDistance1/(this.endpointDistance*this.endpointDistance)
      ).toFixed(this.decimalsRounding)

      this.strelaProvesa2 = parseFloat(
        this.strelaProvesaInput*((this.endpointDistance2)^2)/((this.endpointDistance)^2)
      ).toFixed(this.decimalsRounding)

      console.log(`после: strelaProvesa1=${this.strelaProvesa1}, strelaProvesa2=${this.strelaProvesa2}`)
      var a = this.makeA(this.endpointDistance, this.h, this.cableLengthAtMaxTemperature)
      a = parseFloat(a.toFixed(this.decimalsRounding+2))
      var b = this.makeB(this.W_max, this.endpointDistance, this.cableLengthAtMaxTemperature, this.elasticity_kab, this.strelaProvesa_kab)

      b = parseFloat(b.toFixed(this.decimalsRounding+2))
      // console.log('a =',a, 'and b =',b)

      this.strelaProvesa_max = this.kubicEquasion(a,b)
      this.strelaProvesa_max = parseFloat(this.strelaProvesa_max.toFixed(this.decimalsRounding+2))
      console.log('2.10 максимальная стрела провеса =',this.strelaProvesa_max)
      console.log('2.10 для сравнения обычная стрела провеса =',this.strelaProvesaInput)
    },

    // Максимальная растягивающая нагрузка при наихудших условиях
    task_2_11(){
      this.H_max = (this.W_max * (this.endpointDistance^2)) / (8 * this.strelaProvesa_max)
      this.H_max = parseFloat(this.H_max.toFixed(this.decimalsRounding+2))
      console.log('2.11 Максимальная растягивающая нагрузка при наихудших условиях', this.H_max, 'Н')
      console.log('для сравнения, обычная растягивающая нагрузка из 2.2', this.H_nach, 'Н')
    },

    // Расчет конечной стрелы провеса и нагрузки при нормальных условиях
    task_2_13(){
      console.log('2.13 Расчет конечной стрелы провеса и нагрузки при нормальных условиях')
      // стрела провеса вытяжки
      var a = this.makeA(this.endpointDistance, this.height, this.cableLengthAtMaxTemperature)
      var b = this.makeB(this.W_kab, this.endpointDistance, this.cableLengthAtMaxTemperature, this.elasticity_vit, this.strelaProvesa_kab)
      this.strelaProvesa_n_vit = this.kubicEquasion(a, b).toFixed(this.decimalsRounding)
      console.log('стрела провеса вытяжки', this.strelaProvesa_n_vit)
      // нагрузка после вытяжки (она же усталось металла)
      this.H_n_vit = (
          (this.W_kab * (this.endpointDistance^2)) / (8 * this.strelaProvesa_n_vit)
        ).toFixed(this.decimalsRounding)
      console.log('нагрузка после вытяжки', this.H_n_vit)
    },

    /**
     * получение параметра S, то бишь стрелы провеса
     * @param {number} S                     -
     * @param {number} endpointDistance1     -
     * @param {number} endpointDistance_big  -
     * 
     * @return {number} S_n  -  значение параметра S, формула из пункта 2.4
     */
    get_S1_or_S2(S, endpointDistance1, endpointDistance_big){
      
      // console.error(S, endpointDistance1, endpointDistance_big,
      // '(S*(endpointDistance1^2)/(endpointDistance_big^2))'
      // , (S*(endpointDistance1^2)/(endpointDistance_big^2)))
      // жалкие попытки
      if (S<1 || endpointDistance_big<1 ) {
        S*=100
        endpointDistance_big*=10
      }
      // console.error(S, endpointDistance1, endpointDistance_big,
      // '(S*(endpointDistance1^2)/(endpointDistance_big^2))'
      // , (S*(endpointDistance1^2)/(endpointDistance_big^2)))
      

      return parseFloat(
        S*(endpointDistance1^2)/(endpointDistance_big^2)
      ).toFixed(this.decimalsRounding+2)

    },

    /**
     * 
     * @param W 
     * @param cableLengthNoLoad 
     * @param H 
     */
    another_get_S1_or_S2(W, cableLengthNoLoad, H){
      return parseFloat(
        (W*cableLengthNoLoad*cableLengthNoLoad) / (8*H)
      ).toFixed(this.decimalsRounding+2)
    },

    /**
     * Получение конечной стрелы провеса
     * @param L 
     * @param S1 
     * @param S2 
     * @param L1 
     * @param L2 
     */
    get_strela_provesa(L, S1, S2, L1, L2){
      return (
        L + 4/3 * ( (S1^2 / L1) + (S2^2 / L2) )
      ).toFixed(this.decimalsRounding)
    },

    /**
     * функция составляющая коэффициент A
     * @param {number} L     - длина без учета темпиратуры
     * @param {number} h     - высота установки
     * @param {number} cableLengthAtMaxTemperature  - длина с учетом темпиратуры
     */
    makeA(L, h, cableLengthAtMaxTemperature){
      var result = 3*((L^2) + (((h)^2)/2) - L*cableLengthAtMaxTemperature)/8
      if(this.debug) console.log('a from makeA',result)
      return result
    },   

    /**
     * функция составляющая коэффициент B
     * @param {number} W     - нагрузка на кабель
     * @param {number} L     - длина без учета темпиратуры
     * @param {number} cableLengthAtMaxTemperature  - длина с учетом темпиратуры
     * @param {number} E     - модуль упругости
     * @param {number} S     - стрела провеса
     */
    makeB(W, L, cableLengthAtMaxTemperature, E, S){
      if(this.debug) {
        console.log('W =', W)
        console.log('L =', L)
        console.log('cableLengthAtMaxTemperature =', cableLengthAtMaxTemperature)
        console.log('E =', E)
        console.log('S =', S)
      }
      var result = parseFloat(
        (-3*W*((L)^3)*cableLengthAtMaxTemperature)/(64*E*S)
      )
      if(this.debug) console.log('b from makeB',result)
      return result
    },

    /**
     * решение кубического уровнения для получения S_max
     * Для пункта 2.10
     * @param {number} a - первое вводное
     * @param {number} b - второе вводное
     */
    kubicEquasion(a, b){
      
      var parametr = (a/3)^3 + (-b/2)^2
      console.log('parametr',parametr)
      var answer = 0
      if(parametr>=0){
        answer = (
          (
            (-b/2) + Math.sqrt(parametr)
          )^(1/3) + (
            (-b/2) - Math.sqrt(parametr)
          )^(1/3)
        )
        if(this.debug) console.log(answer, 'вариант 1')
      }
      else{
        answer =  (
          2 * Math.sqrt(-a/3)*Math.cos(
            (1/3)*Math.cos(
              (-b/2)/(
                (-a/3)^(3/2)
              )
            )^(-1)
          )
        )
        if(this.debug) console.log(answer, 'вариант 2')
      }
      return answer 
    },

    /**
     * ветровая нагрузка на кабель в разных режимах для 2.8
     * чисто чтобы не повторять по несколько раз
     * @param W - нормативное ветровое давление, Па, в рассматриваемом режиме: либо макс. ветра, либо макс. гололеда
     */
    windPressure(W){
      if (this.debug){
        console.log('a_w =', this.a_w)
        console.log('K_l =', this.K_l)
        console.log('K_w =', this.K_w)
        console.log('C_x =', this.C_x)
        console.log('d =', this.d)
        console.log('C =', this.C)
      }
      
      
      return parseFloat(
        this.a_w * this.K_l * this.K_w * this.C_x * W * parseFloat(this.d + 2* this.K_i * this.K_d * this.C ) * 0.001  
      ).toFixed(this.decimalsRounding)
    },
  },
};
</script>

