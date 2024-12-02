<template>
  <div
  >
    <!-- заголовок -->
    <div class="header">
      <headerTab title="расчет опор линии связи" v-bind:info="componentInfo"></headerTab>
    </div>


    <!-- ввод требований -->
    <v-card 
      tile
      flat
      class="pa-5" 
    >
      <!-- <h1>Воздушные линии связи</h1> -->
      
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
          @anotherGetVlsSelected="cableWriteSelected"
        />    

      </v-dialog>
      


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
                title="высота установки кабеля"
                desc = "
                таблицы для интерполяции есть не под все значения, но в пределах от 25 до 70 метров всё должно быть окей. 
                В других случаях точность не гарантирована 
                из-за отсутствия таблиц на такой случай
                "
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
          >
            <template v-slot:label>
                <toolbarInfo
                    title="средняя температура эксплуатации (С*)"
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
                    title="темпиретура кабеля в условиях эксплуатации (С*)"
                    imageUrl=''
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
                    title="минимальная температура эксплуатации (С*)"
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
                    title="минимальная температура эксплуатации (С*)"
                />
            </template>                
          </v-text-field>  
        </v-col>
        
      </v-row>

      

      




      


      

      <!-- выбор районов гололеда -->
      <v-dialog
          v-model="chooseZone"
          transition="dialog-bottom-transition"
          width="80%"
          :scrollable="false"
          aria-hidden="true"
        >
        <template v-slot:activator="{ props3 }">
          <p class="mt-5">выбранная зона по ветру: {{ windField }}</p>
          <p class="mt-5">выбранная зона по гололеду: {{ iceField }}</p>
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
          @windGetVlsSelected="windWriteSelected"
          @iceGetVlsSelected="iceWriteSelected"
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
        
        <v-col>
          <v-checkbox
            v-model="debug"
            :label="`debug: ${debug.toString()}`"
          >
          </v-checkbox>
        </v-col>
          
      </v-row>
      



      
    </v-card>


    <!-- результаты вычислений -->
    <v-card 
      class="pa-5 mt-5"
      v-show="started"
    >
        <h2>Результаты расчётов воздушных линий связи:</h2>
        <!-- XD -->
        <p></p>
        <!-- Пункт 2.1 -->
        <p>Вес кабеля: <strong>{{ DotToCommas(W_kab) }} Н/м;</strong> </p>
        <!-- Пункт 2.2 -->
        <p>Растягивающая нагрузка: <strong>{{ DotToCommas(H_nach) }} Н;</strong> </p>
        <!-- Пункт 2.3 -->
        <p>Малый эквивалентный пролет: <strong>{{ DotToCommas(L1) }} м;</strong> </p>
        <!-- Пункт 2.3 -->
        <p>Больший эквивалентный пролет: <strong>{{ DotToCommas(L2) }} м;</strong> </p>
        <!-- Пункт 2.3 -->
        <p>Малая стрела провеса: <strong>{{ DotToCommas(S1) }} м;</strong> </p>
        <!-- Пункт 2.3 -->
        <p>Большая стрела провеса: <strong>{{ DotToCommas(S2) }} м;</strong> </p>
        <!-- Пункт 2.4 -->
        <p>Длина подвешенного кабеля: <strong>{{ DotToCommas(L_kab) }} м;</strong> </p>
        <!-- Пункт 2.5 -->
        <p>Длина кабеля в ненагруженном состоянии: <strong>{{ DotToCommas(L_n0) }} м;</strong> </p>
        <!-- Пункт 2.6 -->
        <p>Длина кабеля в ненагруженном состоянии с учетом температуры: <strong>{{ DotToCommas(L_nk) }} м;</strong> </p>
        <!-- Пункт 2.7 -->
        <p>Вес кабеля при воздействии максимального гололеда: <strong>{{ DotToCommas(W_g) }} кг;</strong> </p>
        <!-- Пункт 2.8 -->
        <p>Ветровая нагрузка на кабель в режиме максимального гололеда: <strong>{{ DotToCommas(W_v_ice) }} Н/м;</strong> </p>
        <!-- Пункт 2.8 -->
        <p>Ветровая нагрузка на кабель в режиме максимального ветра: <strong>{{ DotToCommas(W_v_wind) }} Н/м;</strong> </p>
        <!-- Пункт 2.9 -->
        <p>Максимальная нагрузка, действующая на кабель: <strong>{{ DotToCommas(W_max) }} Н/м;</strong> </p>
        <!-- Пункт 2.10 -->
        <p>Максимальная стрела провиса: <strong>{{ DotToCommas(S_max) }} м;</strong> </p>
        <!-- Пункт 2.11 -->
        <p>Максимальная растягивающая нагрузка при наихудших условиях: <strong>{{ DotToCommas(H_max) }} Н;</strong> </p>
        
        <p><strong>с этого момента пока не доделано</strong></p>
        
        <!-- Пункт 2.13 -->
        <p>Расчет конечной стрелы провеса и нагрузки при нормальных условиях: <strong>{{ DotToCommas(H_n_vit) }} Н;</strong> </p>
        <!-- Пункт 2.14 -->
        <p>расчет стрел провеса и нагрузок при минимальной и максимальной температуре (пока нет полей ввода): 
          при минимальной: <strong>{{ DotToCommas(L_n_minT) }}   С*</strong>, а при максимальной: <strong> {{ DotToCommas(L_n_minT) }} С*</strong> </p>
        <!-- Пункт 2.15 -->
        <p>Расчет стрелы провеса и нагрузки при максимальных условиях (гололед + ветер) после реализации вытяжки: <strong>пока не выведу</strong> </p>
        
        <v-btn
          disabled
          class="mt-2  mr-5"
        >сохранить (WIP)</v-btn>
        <v-btn
          @click="started=false"
          class="mt-2"
        >скрыть</v-btn>
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
      started: false,
      snackbar: false,      // окошко об ошибке
      timeout: 2500,        // время высвечивания окна об ошибке
      errorText: 'Неверно введены данные или они отсутствуют',
      debug: true,

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

      chooseZone: false,

      componentInfo: 
      {
        name: 'Воздушные линии связи',
        incomes: [
        'm, погонный вес кабеля (кг/км)', 
        'L, расстояние между столбами (м)',
        'S, стрела провиса - насколько кабель провисает в нижайшей точке (м)',
        'h – перепад высот между точками подвеса кабеля (м)',
        'T_sr – средняя температура эксплуатации (С*)',
        'T – температура кабеля в условиях эксплуатации (С*)',
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
      
      L: 50,    // расстояние вежду опорами (м)
      S: 1,     // Стрела провеса
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
      E_kab: 4.56,        // модуль упругости кабеля (кН/мм^2)
      E_vit: 3.19,
      E_kon: 4.92, 
      Diameter: 0,        // диаметр кабеля
      S_kab: 116.3,       // сечение кабеля (мм^2)
      TKLR: 0.00001692,   // Темпиратурный коэффициент линейного расширения (1/С*)
                          // БЫТЬ ОСТОРОЖНЫМ СО СТЕПЕНЬЮ ДЕСЯТКИ И НЕ ПЕРЕВОДИТЬ ДО ОТПРАВКИ. Принимающая функция переводит из коэффициента 10^(-6)
      m: 115.6,   // удельный весь кабеля (кг/км)
      S_vit: 3.19,
      S_kon: 4.92,



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
      H_n_vit: 0, // Растягивающая нагрузка вытяжки

      L1: 0,      // Малый эквивалентный пролет
      L2: 0,      // Больший эквивалентный пролет

      L1_max:0,
      L2_max: 0,


      // используется для составления монтажной таблицы
      // L_n_MON_T: 0,  // длина кабеля в ненагруженном состоянии с учетом монтажной температуры
      // L_MON_nk: 0,   // длина кабеля в ненагруженном состоянии при различной температуре
      // T_mon: 0,      // температура кабеля в условиях эксплуатации 

      S1: 0,      //
      S2: 0,      //
      
      S1_max: 0,
      S2_max: 0,

      S1_n_vit: 0,
      S2_n_vit: 0,

      S_n_vit: 0, // Стрела провеса вытяжки

      L_kab: 0,   // Длина подвешенного кабеля
      L_n0: 0,    // Длина кабеля в ненагруженном состоянии
      L_nk: 0,    // Длина кабеля с учетом температуры
      L_max: 0,   // Длина кабеля в нагруженном состоянии

      W_g: 0,     // Вес кабеля при воздействии максимального гололеда
      W_v_ice: 0, // Нагрузка при максимальном гололеде
      W_v_wind: 0,// Нагрузка при максимальном ветре
      S_max: 0,


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


      L_n_minT: 0,      // длина кабеля в ненагруженом состоянии при минимальной темпиратуре
      L_n_maxT: 0,      // длина кабеля в ненагруженом состоянии при максимальной темпиратуре
      L_kab_vit: 0,     // длина кабеля после вытяжки
      L_kab_vit_0: 0,

    };
  },
  methods: {


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
    },


// Из таблицы района гололеда         (за предустановленные взял 1 район)
//    C: 10,       // толщина стенки гололеда (мм)
//    iceField: 1,  // район по гололеду
    iceWriteSelected(data){
      console.log('ice data', `${JSON.stringify(data[0])}`)
        // потом это будет получаться из табличек о климате, но пока так
        this.C = data[0].iceWidth     // нормативное ветровое давление, Па
        this.iceField = data[0].iceField

    },
    
    
    cableWriteSelected(data){
      this.chosenCable = data[0]
      console.log('Final get data', this.chosenCable)
      this.chooseCable = false
      this.setCableParams(this.chosenCable)
    },

    

    setCableParams(chosenCable){
      this.E_kab = chosenCable.L_nach    // модуль упругости кабеля (кН/мм^2)
      // ЗДЕСЬ Я ХЗ, КАКАЯ ИМЕННО УПРУГОСТЬ. ЛИБО НАЧАЛЬНАЯ, ЛИБО КОНЕЧНАЯ, ЛИБО ВЫТЯЖКИ... беру начальную
      this.E_vit = chosenCable.L_feat
      this.E_kon = chosenCable.L_kon
      this.S_kab=chosenCable.Slice
      this.Diameter = chosenCable.Diameter
      this.TKLR = chosenCable.TKLR * 0.000001
      this.m = chosenCable.Weight
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

    
    //Старт
    start() {
      this.started = true
      // как работает этот раздел... хех...
      // Если бы я ещё знал, как он работает.
      // В документе есть много пунктов, я их стал делать по порядку

      if(this.L < 1){
        this.L = 1
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

      // this.task_2_12()

      this.task_2_13()

      this.task_2_14()

      this.task_2_15()

      // this.task_2_16()

    },
    
    // Вес кабеля
    task_2_1(){
      this.W_kab = parseFloat(
        this.m * this.g / 1000
        ).toFixed(this.decimalsRounding)
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

      // this.S1 = (
      //   (this.W_kab * this.L1^2)/(8 * this.H_nach)
      // ).toFixed(this.decimalsRounding+2)
      this.S1 = this.get_S1_or_S2(this.S, this.L1, this.L)
      console.log('2.3 Малая стрела провеса', this.S1)

      // this.S2 = (
      //   (this.W_kab * this.L2^2)/(8 * this.H_nach)
      // ).toFixed(this.decimalsRounding+2)
      this.S2 = this.get_S1_or_S2(this.S, this.L2, this.L)
      console.log('2.3 Большая стрела провеса', this.S2)
    },

    // Длина подвешенного кабеля
    task_2_4(){
      this.L_kab = (
        this.L + 4/3 * ( (this.S1^2 / this.L1) + (this.S2^2 / this.L2) )
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
      this.L_nk = parseFloat(
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
      this.height-=this.S
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
      this.height+=this.S
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
      this.W_v_wind = parseFloat(this.windPressure(this.W_0))
      console.log('ответ для ветра:', this.W_v_wind)

      if(this.debug) console.log('режим максимального гололеда, таблицы нет, потому W_г = 0.25*W_0 =', this.W_0/4)
      this.W_v_ice = parseFloat(this.windPressure(this.W_0/4))
      console.log('ответ для гололеда:', this.W_v_ice)
    },

    // Максимальная нагрузка, действующая на кабель
    task_2_9(){
      this.W_max = parseFloat(
        Math.sqrt(
          (this.W_v_wind*this.W_v_wind) + (this.W_v_ice*this.W_v_ice)
        ).toFixed(this.decimalsRounding)
      )
      console.warn('2.9 Максимальная нагрузка, действующая на кабель ???', this.W_max)
    },

    // Расчет максимальной стрела провиса
    task_2_10(){
      
      // длина кабеля в нагруженом состоянии
      this.L_max = this.L + (4/3)*((this.S1^2/this.L1)+(this.S2^2/this.L2))

      // переопределение S1 и S2
      console.log('переопределение S1 и S2')
      console.log(`до:    S1=${this.S1}, S2=${this.S2}`)

      this.S1 = parseFloat(
        this.S*this.L1*this.L1/(this.L*this.L)
      ).toFixed(this.decimalsRounding)

      this.S2 = parseFloat(
        this.S*((this.L2)^2)/((this.L)^2)
      ).toFixed(this.decimalsRounding)

      console.log(`после: S1=${this.S1}, S2=${this.S2}`)
      var a = this.makeA(this.L, this.h, this.L_nk)
      a = parseFloat(a.toFixed(this.decimalsRounding+2))
      var b = this.makeB(this.W_max, this.L, this.L_nk, this.E_kab, this.S_kab)

      b = parseFloat(b.toFixed(this.decimalsRounding+2))
      // console.log('a =',a, 'and b =',b)

      this.S_max = this.kubicEquasion(a,b)
      this.S_max = parseFloat(this.S_max.toFixed(this.decimalsRounding+2))
      console.log('2.10 максимальная стрела провиса =',this.S_max)
      console.log('2.10 для сравнения обычная стрела провеса =',this.S)
    },

    // Максимальная растягивающая нагрузка при наихудших условиях
    task_2_11(){
      this.H_max = (this.W_max * (this.L^2)) / (8 * this.S_max)
      this.H_max = parseFloat(this.H_max.toFixed(this.decimalsRounding+2))
      console.log('2.11 Максимальная растягивающая нагрузка при наихудших условиях', this.H_max, 'Н')
      console.log('для сравнения, обычная растягивающая нагрузка из 2.2', this.H_nach, 'Н')
    },

    // Расчет монтажной стрелы провеса, нагрузки и монтажной таблицы
    // НЕ ИСПОЛЬЗУЕТСЯ
    task_2_12(){
      console.warn('2.12 Давайте пока не будем, пожалуйста. Можем даже отдельный расчетик под это сделать, но если надо будет')



      this.L_MON_nk = this.L_n0*(1+this.TKLR * (this.T_mon - this.T_sr))
      console.log('L_MON_nk =', this.L_MON_nk)

      var a = 3*((this.L^2) + (((this.h)^2)/2) - this.L*this.L_nk)/8
      a = parseFloat(a.toFixed(this.decimalsRounding+2))

      var b = (-3*this.W_max*((this.L)^3)*this.L_nk)/(64*this.E_kab*this.S_kab)
      b = parseFloat(b.toFixed(this.decimalsRounding+2))
      console.log('a =',a, 'and b =',b)

      this.S_mon = this.kubicEquasion(a, b)

    },

    // Расчет конечной стрелы провеса и нагрузки при нормальных условиях
    task_2_13(){
      console.log('2.13 Расчет конечной стрелы провеса и нагрузки при нормальных условиях')
      
      // this.S1_max = 
      // this.S2_max = 
      // this.L1_max = 
      // this.L2_max = 


      // this.L_kab_k = this.get_strela_provesa(
      //     this.L, this.S1_max, this.S2_max, this.L1_max, this.L2_max
      //   )

      // стрела провеса вытяжки
      var a = this.makeA(this.L, this.h, this.L_nk)
      var b = this.makeB(this.W_kab, this.L, this.L_nk, this.E_vit, this.S_kab)
      // console.log('a =',a, 'and b =',b)
      this.S_n_vit = this.kubicEquasion(a, b)
      // console.log('S_n_vit', this.S_n_vit)


      // нагрузка после вытяжки (она же усталось металла)
      this.H_n_vit = (
          (this.W_kab * (this.L^2)) / (8 * this.S_n_vit)
        ).toFixed(this.decimalsRounding)
      // console.log('H_n_vit', this.H_n_vit)
      // console.log('для сравнения начальная нагрузка', this.H_nach)
      // console.log('для сравнения максимальная нагрузка', this.H_max)

    },

    // расчет стрел провеса и нагрузок при минимальной и максимальной температуре
    task_2_14(){
      this.L_n_minT = (this.L_n0 * (1+ this.TKLR * (this.T_min - this.T_sr))).toFixed(this.decimalsRounding)
      this.L_n_maxT = (this.L_n0 * (1+ this.TKLR * (this.T_max - this.T_sr))).toFixed(this.decimalsRounding)
      console.log('2.14 расчет стрел провеса и нагрузок при минимальной и максимальной температуре', this.L_n_minT, this.L_n_maxT)
    },

    // Расчет стрелы провеса и нагрузки при максимальных условиях (гололед + ветер) после реализации вытяжки
    
    /**
     * функция составляющая коэффициент A
     * @return {number} S1_n_vit     - длина без учета темпиратуры
     * @return {number} S2_n_vit     - длина без учета темпиратуры
     * @return {number} L     - длина без учета темпиратуры
     * @return {number} L     - длина без учета темпиратуры
     * @return {number} L     - длина без учета темпиратуры
     * 
     */
    task_2_15(){




      this.S1_n_vit = this.get_S1_or_S2(this.S, this.L1_n_vit, this.L_kab_vit) 
      this.S2_n_vit = this.get_S1_or_S2(this.S, this.L2_n_vit, this.L_kab_vit) 
      



      this.S1 = parseFloat(
        this.S*this.L1*this.L1/(this.L*this.L)
      ).toFixed(this.decimalsRounding)

      this.S2 = parseFloat(
        this.S*((this.L2)^2)/((this.L)^2)
      ).toFixed(this.decimalsRounding)
      


      this.L_kab_vit = (
          this.L + 4/3 * ( (this.S1_n_vit^2 / this.L1_) + (this.S2_n_vit^2 / this.L2) )
        ).toFixed(this.decimalsRounding)


      this.L_kab_vit_0 = (
          this.L_kab_vit / (1 + (this.H_n_vit / (this.E_kon * this.S_kab)))
        ).toFixed(this.decimalsRounding)
      console.log('2.15 Расчет стрелы провеса и нагрузки при максимальных условиях (гололед + ветер) после реализации вытяжки', this.L_kab_vit_0)
    },

    
    /**
     * получение параметра S, то бишь стрелы провеса
     * @param {number} S      -
     * @param {number} L1     -
     * @param {number} L_big  -
     * 
     * @returns {number}      значение параметра S, формула из пункта 2.4
     */
    get_S1_or_S2(S, L1, L_big){
      return parseFloat(
        S*(L1^2)/(L_big^2)
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
     * @param {number} L_nk  - длина с учетом темпиратуры
     */
    makeA(L, h, L_nk){
      var result = 3*((L^2) + (((h)^2)/2) - L*L_nk)/8
      if(this.debug) console.log('a from makeA',result)
      return result
    },
    

    /**
     * функция составляющая коэффициент B
     * @param {number} W     - нагрузка на кабель
     * @param {number} L     - длина без учета темпиратуры
     * @param {number} L_nk  - длина с учетом темпиратуры
     * @param {number} E     - модуль упругости
     * @param {number} S     - стрела провеса
     */
    makeB(W, L, L_nk, E, S){
      if(this.debug) {
        console.log('W =', W)
        console.log('L =', L)
        console.log('L_nk =', L_nk)
        console.log('E =', E)
        console.log('S =', S)
      }
      var result = parseFloat(
        (-3*W*((L)^3)*L_nk)/(64*E*S)
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


    // чисто для своих проверок, привязано к своей кнопке
    test(){
      console.log("lets test")
      console.log(this.W)
    },
  },
};
</script>

