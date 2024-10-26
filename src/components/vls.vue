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
                title="высота расположения приведенного центра тяжести проводов, троссов и средних точек"
                imageUrl=''
                desc = "
                Выставлять в пределах от 25 до 100 метров. 
                В других случаях точность не гарантирована 
                из-за отсутствия таблиц на такой случай
                "
            />
        </template>                
      </v-text-field> 
      

      




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
              <p class="mt-5">выбранный кабель: {{ chosenCable }}</p>
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
          @anotherGetVlsSelected="NewWriteSelected"
        />    

      </v-dialog>


      

      <!-- выбор районов гололеда -->
      <v-dialog
          v-model="chooseZone"
          transition="dialog-bottom-transition"
          width="80%"
          :scrollable="false"
          aria-hidden="true"
        >
        <template v-slot:activator="{ props3 }">
            <p class="mt-5">выбранная зона: {{ chosenZone }}</p>
            <!-- здесь можно сделать сам вывод выбранных значений -->
            <v-btn
              class="mt-5"
              width="100%"
              color="primary"
              v-bind="props3"
              @click="chooseZone = true"
              ><v-icon>mdi-cable-data</v-icon>
              выбрать зону
            </v-btn>
            
          </template>

        <climateTable
          
        />    

      </v-dialog>
      
        
      <!-- кнопки -->
      <v-row class="mt-5">
        <v-col>
          <v-btn 
            @click="start" 
            color="primary" 
            width="100%"
          >Старт</v-btn>
        </v-col>
        <v-col>
          <v-btn 
            @click="test" 
            color="primary" 
            width="100%"
          >тест</v-btn>
        </v-col>
          
      </v-row>
      



      
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
import climateTable from './ui/forVls/climateTable.vue';

export default {
  name: "vls_vue",
  components: {
    headerTab,
    tableSpace,
    climateTable,
    toolbarInfo
  },
  data() {
    return {
      snackbar: false,      // окошко об ошибке
      timeout: 2500,        // время высвечивания окна об ошибке
      errorText: 'Неверно введены данные или они отсутствуют',

      selected: '',

      chooseCable: false,
      chosenCable: {
        Mark: 'ДПТ до 48 ОВ (6х8)',
        MDRN: 6, 
        MRN: 1.5, 
        MPR: 8.48, 
        Weight: 115.6, 
        Diameter: 12.2, 
        Slice: 116.3, 
        L_nach: 4.56, 
        L_kon: 4.92, 
        L_feat: 3.19, 
        TLKR: 16.92
      },
      
      chooseZone: false,
      chosenZone: "1",

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


      height: 35,


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

      heightTable: [
        {
          height: 25,
          K_i: 1,
          d: 10,
          K_d: 1,
        },
        {
          height: 30,
          K_i: 1.4,
          d: 20,
          K_d: 0.9,
        },
        {
          height: 50,
          K_i: 1.6,
          d: 30,
          K_d: 0.8,
        },
      ],



  // Из таблицы района гололеда         (за предустановленные взял 1 район)
      C: 10,       // толщина стенки гололеда (мм)

  // Из таблицы района по ветру         (за предустановленные взял 1 район)
      W_0: 400,   // Нормативно ветровое давление
      v_0: 25,    // скорость ветра

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
        
      ]

    };
  },
  methods: {

    NewWriteSelected(data){
      this.chosenCable = data[0]
      console.log('Final get data', this.chosenCable)
      this.chooseCable = false

      this.E_kab=this.chosenCable.L_nach    // модуль упругости кабеля (кН/мм^2)
      // ЗДЕСЬ Я ХЗ, КАКАЯ ИМЕННО УПРУГОСТЬ. ЛИБО НАЧАЛЬНАЯ, ЛИБО КОНЕЧНАЯ, ЛИБО ВЫТЯЖКИ... беру начальную
      this.S_kab=this.chosenCable.Slice
      this.TKLR = this.chosenCable.TLKR * 0.000001
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

      var debug = 1
      if(debug){
        console.log(`минимальное значение ${SearchThrough} `, minValueX)
        console.log(`минимальное значение ${searchableValue} `, minValueSearched)

        console.log(`максимальное значение ${SearchThrough} `, maxValueX)
        console.log(`максимальное значение ${searchableValue} `, maxValueSearched)
      }
      console.log('введенное значение', value)

      // ниже минимума
      if (value <= minValueX){
        console.error('введено меньше минимального', value, '<', minValueX)
        console.log('ответ:', minValueSearched)
        return minValueSearched
      }
      // превышение максимума
      if (value >= maxValueX){
        console.error('введено больше максимального', value, '>', maxValueX)
        console.log('ответ:', maxValueSearched)
        return maxValueSearched
      }

      // вычисления с учетом длины массива
      console.log(`предцикловый вывод, входное значение ${value}, проверить минимальное значение ${dataTable[0][SearchThrough]}, длина массива = ${dataTable.length}`)
      for (var i = 0; i<dataTable.length; i++){
        console.log(`итерация ${i}, значение ${value}, сравниваем с ${dataTable[i][SearchThrough]}`)
        if (value < dataTable[i][SearchThrough]){
          var x1 = dataTable[i-1][SearchThrough]
          var x2 = dataTable[i][SearchThrough]

          var y1 = dataTable[i-1][searchableValue]
          var y2 = dataTable[i][searchableValue]

          var result = this.interpolateFormula(value, x1, x2, y1, y2)
          console.log( searchableValue, '=', result,' как результат интерполяции (',value, x1, x2, y1, y2,')')
          return result
        }
      }
    },

    /**
     * 
     * @param {number} value  координата по оси Х, по которой нужно найти Y
     * @param {number} x1     значение по оси Х меньше искомой
     * @param {number} x2     значение по оси Х больше искомой
     * @param {number} y1     значение по оси Y меньше искомой
     * @param {number} y2     значение по оси Y больше искомой
     */
    interpolateFormula(value, x1, x2, y1, y2){
      return (y1 + (value-x1)* ((y2-y1)/(x2-x1))).toFixed(this.decimalsRounding+1)
    },

    
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

      this.task_2_8()

    },
    
    // Вес кабеля
    task_2_1(){
      this.W_kab = parseFloat(this.m * this.g / 1000).toFixed(this.decimalsRounding)
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

    // здесь будет уже упоминание таблицы
    // Вес кабеля при воздействии максимального гололеда
    task_2_7(){
      // для получения нужных Ki, Kd и d
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

      this.W_g = parseFloat(
        parseFloat(this.W_kab) + 
        parseFloat(this.Ro_L * this.g * Math.PI * this.K_i * this.K_d * this.C * (this.d + this.C))
        ).toFixed(this.decimalsRounding)
      // console.log('2.7 проверка', parseFloat(this.W_kab))
      // console.log('2.7 проверка', this.Ro_L)
      // console.log('2.7 проверка', this.g)
      // console.log('2.7 проверка', Math.PI)
      // console.log('2.7 проверка', this.K_i)
      // console.log('2.7 проверка', this.K_d)
      // console.log('2.7 проверка', this.C)
      // console.log('2.7 проверка', this.d)
      // console.log('2.7 проверка', parseFloat(this.d + this.C))
      // console.log('2.7 проверка', parseFloat(this.Ro_L * this.g * Math.PI * this.K_i * this.K_d * this.C * (this.d + this.C)).toFixed(this.decimalsRounding))
      
      console.log('2.7 Вес кабеля при воздействии максимального гололеда, кг', this.W_g)
    },

    // Ветровая нагрузка на кабель при гололеде
    task_2_8(){
      console.log("2.8 проверяем числа")
      // this.interpolateK_l(this.height)
      this.K_l = this.interpolateUniversal(
          this.height, 
          this.K_lTable, 
          'K_l', 
          'height'
        )
      console.log(this.K_l, this.height)

      // this.interpolateK_w(this.height, this.terrainType)
      
      // K_w - по табличке, входным параметром является - this.height
      // a_w - по табличке ветрового давления
      /*
      W - Нормативное ветровое давление по району

        При расчете в режиме максимального ветра - по табличке (W_0)
        При расчете в режиме максимального гололеда - по формуле 
                    со скоростью ветра при гололеде v_г

                    W = v^2 / 1.6

                    если v_г неизвестен, то
                    W_г = 0.25 * W_0
      */
      this.W_v = this.a_w * this.K_l * this.K_w * this.C_x * this.W * (this.d_kab + 2* this.K_i * this.K_d * this.C ) * 0.001  
    },




    // чисто для своих проверок, привязано к своей кнопке
    test(){
      console.log("lets test")
      console.log(
        this.interpolateUniversal(
          this.height, 
          this.heightTable, 
          'K_i', 
          'height'
        )
      )
    }

  },
};
</script>

