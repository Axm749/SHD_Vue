<template>
  <div>
    <!-- заголовок -->
    <div class="header">
        <headerTab 
            title="расчет времени работы оборудования от аккумуляторных батарей"
            v-bind:info = "componentInfo"
        ></headerTab>
    </div>
    
    <!-- поля ввода -->
    <v-card class="pa-5" tile>
      

      
      <v-text-field
          type="number"
          outlined
          required
          clearable
          label="Напряжение аккумуляторных батарей (В)"
          :rules="rule"
          hide-details="auto"
          v-model.number="voultage"
          class="mt-5"
      />
      
      <v-text-field
          type="number"
          outlined
          required
          clearable
          label="Емкость аккумуляторной батареи (А/ч)"
          :rules="rule"
          hide-details="auto"
          v-model.number="capacity"
          class="mt-5"
      />
      <v-text-field
          type="number"
          outlined
          clearable
          required
          label="Кол-во аккумуляторных батарей в цепи (шт.)"
          :rules="rule"
          hide-details="auto"
          v-model.number="batteries"
          class="mt-5"
      />
      <v-text-field
          type="number"
          outlined
          required
          clearable
          label="КПД преобразователя в долях (0,8 - 1,0)"
          :rules="rule"
          hide-details="auto"
          v-model.number="kpd"
          class="mt-5"
      />
      <v-text-field
          type="number"
          :disabled="self === false "
          outlined
          clearable
          label="Макс. суммарная мощность нагрузой (Вт)"
          :rules="rule"
          hide-details="auto"
          v-model.number="power"
          class="mt-5"
      />
      <v-checkbox 
          info
          hide-details
          label="Ввести вручную?" 
          v-model="self"
          class="mt-5"
      />
      <v-btn 
          @click="start" 
          color="primary"
          class="mt-5"
          width="100%"
      >Старт</v-btn>
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
          ОК
          </v-btn>
      </template>
      </v-snackbar>

  <v-card 
      class="pa-5 mt-5"
      v-show="started"
  >
      <h2>Время работы устройства:</h2>
      <p><strong>{{ result }}, ч.</strong></p>
      <v-btn
        @click="started=false"
        class="mt-2"
      >скрыть</v-btn>
 </v-card>

 
</div>
</template>

<script>
import headerTab from './ui/commonUi/header.vue';
  
    
export default{
    name: 'power_vue',
    components: {
        headerTab,
        },
    data: () =>({
        voultage: '...', //Напряжение аккумуляторных батарей
        power: '...',    //Мощность для питания СХД
        kpd: '...',      //КПД баттарей
        batteries: '...',//Кол-во батарей
        capacity: '...', //Ёмкость батарей
        result:0,        //Параметр, отвечающий за вывод результата раздела электропитания
        self: false,     //Параметр, отвечающий за ручной ввод мощности для питания СХД
        started: false,  //Параметр, отвечающий за вывод результатов работы электропитания после нажатия "Старт"
        rule: [
            value => !!value || 'Необходимо заполнить это поле.',
        ],                       //Правила для текстовых полей
        snackbar: false,      // окошко об ошибке
        timeout: 2500,
        errorText: 'Неверно введены данные или они отсутствуют',


        // для пункта с информацией о модуле
      componentInfo: {
        name: 'расчет времени работы оборудования от аккумуляторных батарей',
        incomes: [
          'число устройств выдающих усреднённое значение битрейта;', 
          'Количество дней (суток) на протяжении которых информация должна храниться;', 
          'Значение битрейта выбирается из предустановленных, рассчитывается в соответствующем модуле для видеокамер или вводится вручную при необходимости.',
        ],
        outcomes: [
          'Число узлов необходимое для удовлетворения данных условий (шт.);',
          'Число узлов с учетом резервирования (на 2 больше);',
          'Объём хранимой информации в Тбайт (Терабайт) и в TiB (Тебибайт).'], 
        description: `Модуль расчета объёма системы хранения данных для систем с усреднённым входным битрейтом. В ответ на введенные требования, определяет число узлов, необходимых для их выполнения. Параметры узлов можно задавать вручную, а также предусмотрены гиперконвергентные системы.`,
        disclaimers: [
          'Гиперконвергентные системы - СХД, способные не только хранить информацию, но и обрабатывать её в реальном времени. Используют больше вычислительных ресурсов и потому зачастую делаются меньшего объёма, что приводит к увеличению числа узлов для покрытия того же объёма информации;',
          'Округления идут в большую сторону, предполагается наихудший сценарий;',
          "Все значения должны быть не отрицательными, а время хранения больше нуля;",
          "Для обычных и гиперконергентных систем, предустановленные значения объёмов стандартных узлов отличаются:",
          "Для гиперконвергентных - 7 дисков по 4 Тб, а для обычных - 15 дисков по 8 Тб.",
          "Другие характеристики узлов указаны в документации."
        ],
        constants: [
          {
            name: 'Объёмы стандартных узлов:', 
            massive: [ 
                'Для гиперконвергентных - 7 дисков по 4 Тб',
                'для дисковых систем - 15 дисков по 8 Тб'
              ]
          },
          {
            name: 'Усредненный битрейт в режимах:', 
            massive: [ 
                'Локальная вычислительная сеть    - 5 мбит/с; ',
                'Системы телефонии (ip-телефония) - 0,1 мбит/с; ',
                'Системы видеонаблюдения          - 8 мбит/с',
              ]
          },
          {
            name: 'Набор серверов в гиперконвергентных системах:', 
            massive: [ 
                'Сервер управления                    - 300 Гб, 1 шт; ',
                'Сервер архивного управления          - 300 Гб, 1 шт; ',
                'Сервер сопряжения                    - 600 Гб, 3 шт; ',
                'Сервер ситуационного видеонаблюдения - 300 Гб, 1 шт; ',
                'Сервер другой                        - 7680 Гб, 3 шт;',
              ]
          },
        ]
      },

    }),
    methods:{
        start(){
            
            this.started = true
            if(this.self){ //Расчёт при ручном вводе
                this.result =0.1 * Math.ceil(
                    10*this.voultage*this.capacity
                    *this.batteries*this.kpd
                    *0.85/this.power)

                console.log("Время работы устройства:   ", this.result, " ч");
            }else{         //Расчёт при автоматическом вводе
                this.power = ''
                this.power = localStorage.getItem('wats')
                this.result = 0.1 * Math.ceil(
                    10*this.voultage*this.capacity
                    *this.batteries*this.kpd
                    *0.85/this.power
                    )
                console.log("Время работы устройства:   ", this.result, " ч"); 
            }
            if (!this.result || this.result== Infinity){
                this.snackbar = true
                this.started = false
                return
            }
        },              //Ф-ция старт
        getPower(){
            return this.power = localStorage.getItem('wats')
        }               //Ф-ция, отвечающая за приём параметра мощности из раздела СХД
      },
      mounted(){
          this.getPower()
      }
  }
</script>

<style>

</style>