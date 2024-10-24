<template>
  <div
  >
    <!-- заголовок -->
    <div class="header">
      <headerTab title="Воздушные линии связи" v-bind:info="componentInfo"></headerTab>
    </div>


    <!-- ввод требований -->
    <v-card 
      tile
      flat
      class="pa-5" 
    >
      <!-- <h1>Воздушные линии связи</h1> -->
      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable
        :rules="rule"
        hide-details="auto"
        v-model.number="m"
        class="mt-5"
      >
        <template v-slot:label>
            <toolbarInfo
                title="масса кабеля (кг/км)"
                imageUrl=''
            />
        </template>                
      </v-text-field>

      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable
        :rules="rule"
        hide-details="auto"
        v-model.number="L"
        class="mt-5"
      >
        <template v-slot:label>
            <toolbarInfo
                title="расстояние вежду опорами (м)"
                imageUrl=''
            />
        </template>                
      </v-text-field>

      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable
        :rules="rule"
        hide-details="auto"
        v-model.number="S"
        class="mt-5"
      >
        <template v-slot:label>
            <toolbarInfo
                title="Стрела провеса, м"
                imageUrl=''
                v-bind:desc = "componentInfo.incomes[2]"
            />
        </template>                
      </v-text-field>

      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable
        :rules="rule"
        hide-details="auto"
        v-model.number="h"
        class="mt-5"
      >
        <template v-slot:label>
            <toolbarInfo
                title="перепад высот соседних опор, м"
                imageUrl=''
                v-bind:desc = "componentInfo.incomes[3]"
            />
        </template>                
      </v-text-field>

      <v-row class="mt-5">
        
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
          >
            <template v-slot:label>
                <toolbarInfo
                    title="средняя темпиратура эксплуатации (С*)"
                    imageUrl=''
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
            hide-details="auto"
            v-model.number="T"
          >
            <template v-slot:label>
                <toolbarInfo
                    title="темпиратура кабеля в условиях эксплуатации (С*)"
                    imageUrl=''
                    v-bind:desc = "componentInfo.incomes[5]"
                />
            </template>                
          </v-text-field>  
        </v-col>
        
      </v-row>
      

      




      <!-- выбор марки кабеля -->
      <v-dialog
          v-model="chooseCable"
          transition="dialog-bottom-transition"
          width="80%"
          :scrollable="false"
          aria-hidden="true"
        >
        <template v-slot:activator="{ props2 }">
            <p class="mt-5">выбранный кабель: {{ chosenCable }}</p>
            <!-- здесь можно сделать сам вывод выбранных значений -->
            <v-btn
              class="mt-5"
              width="100%"
              color="primary"
              v-bind="props2"
              @click="chooseCable = true"
              ><v-icon>mdi-cable-data</v-icon>
              выбрать кабель
            </v-btn>
            
          </template>

        <tableSpace/>    

      </v-dialog>
      
        
      <!-- старт -->
      <v-btn 
        @click="start" 
        color="primary" 
        class="mt-5"
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

export default {
  name: "vls_vue",
  components: {
    headerTab,
    tableSpace,
    toolbarInfo
  },
  data() {
    return {
      snackbar: false,      // окошко об ошибке
      timeout: 2500,        // время высвечивания окна об ошибке
      errorText: 'Неверно введены данные или они отсутствуют',

      chooseCable: false,
      chosenCable: 'ДПТ до 48 ОВ (6х8)',

      componentInfo: 
      {
        name: 'Воздушные линии связи',
        incomes: [
        'm, погонный вес кабеля (кг/км)', 
        'L, расстояние между столбами (м)',
        'S, стрела провиса - насколько кабель провисает в нижайшей точке (м)',
        'h – перепад высот между точками подвеса кабеля (м)',
        'T_sr – средняя темпиратура эксплуатации (С*)',
        'T – темпиратура кабеля в условиях эксплуатации (С*)',
        ],
        outcomes: [
        'выходная величина',
        ], 
        description: 'Описание',
        disclaimers: [
        'округления идут в большую сторону, предполагается наихудший сценарий'
        ]
      },
      
      rule: [
          value => !!value || 'Необходимо заполнить это поле.',
      ],                              //Правила для текстовых полей

      decimalsRounding: 2, // число знаков после запятой при округлении. Где-то может расширяться или снижаться



      // а вот отсюда уже нужные для вычисления переменные

      // константы:
      g: 9.8,         // ускорение свободного падения (м/с^2)
      Ro_L: 0.0009,   // объёмная масса гололеда (кг/см^3)
                      // БЫТЬ ОСТОРОЖНЫМ СО СТЕПЕНЬЮ ДЕСЯТКИ И ПЕРЕВОДИТЬ ДО ОТПРАВКИ

      // входные параметры
      m: 100,   // удельный весь кабеля (кг/км)
      L: 20,    // расстояние вежду опорами (м)
      S: 5,     // Стрела провеса
      h: 15,    // Перепад высот между точками подвеса кабеля

      T_sr: 24, // Средняя темпиратура эксплуатации
      T: 20,    // темпиратура кабеля в условиях эксплуатации




  // Получаемое из таблички кабеля
  // для примера берётся ДПТ до 48ОВ, с МДРН = 4
      E_kab: 4.56,        // модуль упругости кабеля (кН/мм^2)
      // ЗДЕСЬ Я ХЗ, КАКАЯ ИМЕННО УПРУГОСТЬ. ЛИБО НАЧАЛЬНАЯ, ЛИБО КОНЕЧНАЯ, ЛИБО ВЫТЯЖКИ... беру начальную
      S_kab: 116.3,       // сечение кабеля (мм^2)
      TKLR: 0.00001692,   // Темпиратурный коэффициент линейного расширения (1/С*)
                          // БЫТЬ ОСТОРОЖНЫМ СО СТЕПЕНЬЮ ДЕСЯТКИ И ПЕРЕВОДИТЬ ДО ОТПРАВКИ


  // Из таблички о высоте расположения  (за предустановленные взял 25м)
      K_i: 1,     // Коэффициент изменения толщины стенки гололеда по высоте над поверхностью земли
      d: 10,      // диаметр кабеля (мм)
      K_d: 1,     // Коэффициент изменение толщины стенки гололеда в зависимости от диаметра провода (троса)


  // Из таблицы района гололеда         (за предустановленные взял 1 район)
      C: 10,       // толщина стенки гололеда (мм)


      // нужные для внутренних расчетов
      W_kab: 0,   // Вес кабеля (Н/м)
      H_nach: 0,  // Растягивающая нагрузка (Н)

      L1: 0,      // Малый эквивалентный пролет
      L2: 0,      // Больший эквивалентный пролет

      S1: 0,      //
      S2: 0,      //

      L_kab: 0,   // Длина подвешенного кабеля
      L_n0: 0,    // Длина кабеля в ненагруженном состоянии

      W_g: 0,     // Вес кабеля при воздействии максимального гололеда


    };
  },
  methods: {
    
    
    //Старт
    start() {
      // как работает этот раздел... хех...
      // Если бы я ещё знал, как он работает.
      // В документе есть много пунктов, я их стал делать по порядку

      this.task_2_1()

      this.task_2_2()

      this.task_2_3()

      this.task_2_4()

      this.task_2_5()

      this.task_2_6()

      this.task_2_7()

    },
    
    // Вес кабеля
    task_2_1(){
      this.W_kab = (this.m * this.g / 1000).toFixed(this.decimalsRounding)
      console.log('2.1 вес кабеля, Н/м', this.W_kab)
    },

    // Растягивающая нагрузка, действующая на кабель
    task_2_2(){
      this.H_nach = ((this.W_kab * this.L^2) / ( 8 * this.S )).toFixed(this.decimalsRounding+2)
      console.log('2.2 Растягивающая нагрузка, Н', this.H_nach)
    },

    // Перепад высот между опорами
    task_2_3(){
      this.L1 = (
        this.L - (2 * this.h * this.H_nach)/(this.W_kab * this.L)
      ).toFixed(this.decimalsRounding+2)
      console.log('2.3 Малый эквивалентный пролет', this.L1)

      this.L2 = (
        this.L + (2 * this.h * this.H_nach)/(this.W_kab * this.L)
      ).toFixed(this.decimalsRounding+2)
      console.log('2.3 Больший эквивалентный пролет', this.L2)

      this.S1 = (
        (this.W_kab * this.L1^2)/(8 * this.H_nach)
      ).toFixed(this.decimalsRounding+2)
      console.log('2.3 Малая стрела провеса', this.S1)

      this.S2 = (
        (this.W_kab * this.L2^2)/(8 * this.H_nach)
      ).toFixed(this.decimalsRounding+2)
      console.log('2.3 Большая стрела провеса', this.S2)
    },

    // Длина подвешенного кабеля
    task_2_4(){
      this.L_kab = (
        this.L + 4/3 * ( this.S1^2 / this.L1 + this.S2^2 / this.L2 )
      ).toFixed(this.decimalsRounding)
      console.log('2.4 длина подвешенного кабеля, м', this.L_kab)
    },

    // Длина кабеля в ненагруженном состоянии
    task_2_5(){
      this.L_n0 = (
        this.L_kab / (1 + (this.H_nach / (this.E_kab * this.S_kab)))
      ).toFixed(this.decimalsRounding)
      console.log('2.5 Длина кабеля в ненагруженном состоянии, м', this.L_n0)
    },

    // Длина кабеля в ненагруженном состоянии с учетом температуры
    task_2_6(){
      this.L_nk = (
        this.L_n0 * (1 + this.TKLR * (this.T - this.T_sr))
      ).toFixed(this.decimalsRounding)
      // console.log('2.6 ТКЛР', this.TKLR)
      // console.log('2.6 проверка', 1 + this.TKLR * (this.T - this.T_sr))
      console.log('2.6 Длина кабеля в ненагруженном состоянии с учетом температуры, м', this.L_nk)
    },

    // Вес кабеля при воздействии максимального гололеда
    task_2_7(){
      this.W_g = this.W_kab + this.Ro_L * this.g * 3.14159 * this.K_i * this.K_d * this.C * (this.d + this.C)
      console.log('2.7 проверка', this.Ro_L * this.g * 3.14159 * this.K_i * this.K_d * this.C * (this.d + this.C))
      console.log('2.7 проверка', this.W_kab)
      console.log('2.7 Вес кабеля при воздействии максимального гололеда, кг', this.W_g)
    },

  },
};
</script>

