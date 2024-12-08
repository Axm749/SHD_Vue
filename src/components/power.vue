<template>
  <div>
    <!-- заголовок -->
    <div class="header">
        <headerTab 
            title="Расчет времени работы оборудования от аккумуляторных батарей"
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
        >
            <template v-slot:label>
            <toolbarInfo
                title="Напряжение аккумуляторных батарей (В)"
                desc = "Номинальное напряжение аккумуляторных батарей в нормальных условиях и полном заряде, измеряется в вольтах."
            />
            </template> 
        
        </v-text-field>
      
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
        >
            <template v-slot:label>
            <toolbarInfo
                title="Емкость аккумуляторной батареи (А/ч)"
                desc = "Объём энергии, способных хранится в аккумуляторной батареии при нормальных условиях, измеряется в Амперах на час."
            />
            </template> 

        </v-text-field>

        <v-text-field
          type="number"
          outlined
          clearable
          required
          label="Кол-во батарей в цепи (шт.)"
          :rules="rule"
          hide-details="auto"
          v-model.number="batteries"
          class="mt-5"
          >
            <template v-slot:label>
            <toolbarInfo
                title="Количество батарей в цепи (шт.)"
                desc = "Количество аккумуляторных батарей в цепи. Конфигурация является сочетанием последовательных и параллельно включенных аккумуляторов. Здесь указывается количество параллельно соединенных батарей (рядов). Измеряется в штуках."
            />
            </template> 

        </v-text-field>

        <v-text-field
          type="number"
          outlined
          required
          clearable
          label="КПД преобразователя (0,8 - 1,0)"
          :rules="rule"
          hide-details="auto"
          v-model.number="kpd"
          class="mt-5"
        >
            <template v-slot:label>
            <toolbarInfo
                title="КПД преобразователя в долях (0,8 - 1,0)"
                desc = "Мера того, какая доля от хранимой энергии будет передана на нагрзку, где 0,8 - 80%, а 1,0 - 100%."
            />
            </template> 

        </v-text-field>

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
        >
            <template v-slot:label>
            <toolbarInfo
                title="Макс. суммарная мощность нагрузой (Вт)"
                desc = "Максимальная суммарная мощность нагрузой. Измеряется в ваттах. Вставляется автоматически на основании числа узлов и их типа, рассчитанных в разделе системы хранения данных. Перевод происходит по следующему принципу: дисковые узлы = 700 Вт на узел, а гиперконвергентные = 1000 Вт на узел."
            />
            </template> 

        </v-text-field>
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
      <p><strong>{{ result.toFixed(3) }}</strong>, ч. или 
      <strong>{{ (result*60).toFixed(0) }}</strong>, мин.</p>
      <v-btn
        @click="started=false"
        class="mt-2"
      >скрыть</v-btn>
 </v-card>

 
</div>
</template>

<script>
import headerTab from './ui/commonUi/header.vue';
import toolbarInfo from './ui/commonUi/tooltip.vue';
    
export default{
    name: 'power_vue',
    components: {
        headerTab,
        toolbarInfo
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
            'Напряжение аккумуляторных батарей (В);', 
            'Емкость аккумуляторной батареи (А/ч);', 
            'Кол-во батарей в цепи (шт.);', 
            'КПД преобразователя (0,8 - 1,0).',
            ],
            outcomes: [
            'Время автономной работы системы в часах и минутах;'], 
            description: `В этом разделе программы рассчитывается время работы СХД от аккумуляторных батарей. За входные значения первостепенно значится число узлов, что можно рассчитать в разделе систем хранения данных, тип этих узлов (дисковый или гиперконвергентный), напряжение, ёмкость, количество и кпд аккумуляторных батарей. Также в учет берутся коэффициенты глубины разряда и доступной ёмкости. Подразумевается, что используется ИБП, который при отключении питания из сети моментально переключается на питание постоянным напряжением от батарей.`,
            disclaimers: [
            'Модуль рассчитывает время автономной работы по формуле: ',
            't = Uаб * Cак * К * h * Kp * Kg * (Q / Pнагр), где:',
            't - время работы оборудования от аккумуляторных батарей [ч];',
            "Uaб - напряжение аккумуляторных батарей [В];",
            "Сак - емкость аккумуляторной батареи [A*ч];",
            "K - количество аккумуляторных батарей в цепи [шт];",
            "h - КПД преобразователя, так как преобразование из постоянного в переменное напряжение отсутствует;",
            "Pнагр – максимальная суммарная мощность нагрузок [Вт], которая является числом узлов умноженным на максимальную мощность одного узла;",
            "округление происходит в большую сторону;",
            
            ],
            constants: [
            {
                name: 'Kg-коэффициент доступной емкости (зависит от режима разряда и температуры, вычисляется с использованием таблицы разряда постоянным током, при температуре 25°С и нижним значением напряжения разряда батареи 1,75В на элемент):', 
                massive: [ 
                    'в данном расчете 0,85'
                ]
            },
            {
                name: 'Kр-коэффициент глубины разряда:', 
                massive: [ 
                    'в данном расчете 0,85'
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