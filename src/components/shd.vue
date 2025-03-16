<template>
  <div
  >
    <!-- заголовок -->
    <div class="header">
      <headerTab 
        title="Расчет системы хранения данных"
        v-bind:info = "componentInfo"
      ></headerTab>
    </div>
    
    <!-- ввод требований -->
    <v-card 
      class="pa-5 rounded-b"
      tile
      flat 
    >

      <v-text-field
        outlined
        type="number"
        required
        clearable
        label="Количество устройств"
        :rules="rule"
        hide-details="auto"
        v-model.number="users"
        class="mt-5"
      >
        <template v-slot:label>
          <toolbarInfo
            title="Количество устройств (шт)"
            desc = "число устройств, отправляющий информацию на вход системы хранения данных. Они все принимаются идентичными, усредненными"
          />
        </template> 
      </v-text-field>
      
      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable
        label="Дни хранения"
        :rules="rule"
        hide-details="auto"
        v-model.number="days"
        class="mt-5"
      >
        <template v-slot:label>
          <toolbarInfo
            title="Дни хранения (сутки)"
            desc = "число дней, которое поступающая информация должна сохраняться внутри системы хранения данных. Далее, она удаляется, освобождая память под новые данные"
          />
        </template> 
      
      </v-text-field>


      <v-text-field
        flat
        type="number"
        required
        outlined
        clearable
        label="неизвестно что "
        :rules="rule"
        hide-details="auto"
        v-model.number="strange"
        class="mt-5"
      >
        <template v-slot:label>
          <toolbarInfo
            title="я хз что"
            desc = "я хз, что это за число, но оно имеет значение 37 и в расчетах фигурирует на третьем узле при расчете резерва N+1"
          />
        </template> 
      
      </v-text-field>




      <!-- стандратный ли узел -->
      <v-checkbox
        info
        hide-details
        label="Узел не стандартный?"
        v-model="standart_discs"
        class="mt-5"
      />
      <!-- гиперконвергентность -->
      <v-checkbox
        info
        hide-details
        label="Система гиперконвергентна?"
        v-model="converg"
        class="mt-5"
      />

      <!-- если диск нестандартный -->
      <template v-if="standart_discs">
        <v-dialog
          v-model="dialog2"
          transition="dialog-bottom-transition"
          min-width="80%"
          :scrollable="false"
          aria-hidden="true"
        >
        <template v-slot:activator="{ props2 }">
            <v-btn
              class="mt-2 mt-5"
              width="100%"
              color="primary"
              v-bind="props2"
              @click="getStores"
              ><v-icon>mdi-cog</v-icon>
              Настройки узла
            </v-btn>
          </template>

          <div class="module_bg">
            <v-card
              class="pa-5"
            >

              <h1>параметры дисковых хранилищ узлов</h1>

              <v-text-field
                flat
                type="number"
                required
                outlined
                clearable
                label="Объём диска (Тбайт)"
                :rules="rule"
                hide-details="auto"
                v-model.number="disc_capacity"
                class="mt-5"
              >
                <template v-slot:label>
                  <toolbarInfo
                    title="Объём диска (Тбайт)"
                    desc = "Мера того, сколько информации способен вместить один диск данного узла."
                  />
                </template> 
              </v-text-field>
              
              <v-text-field
                flat
                type="number"
                required
                outlined
                clearable
                label="Количество дисков (шт)"
                :rules="rule"
                hide-details="auto"
                v-model.number="disc_count"
                class="mt-5"
              >
                <template v-slot:label>
                  <toolbarInfo
                    title="Количество дисков (шт)"
                    desc = "Число дисков указанного выше объёма, установленных внутри одного узла."
                  />
                </template> 
              </v-text-field>
              
              <v-btn
                class="mt-2 mt-5"
                width="100%"
                color="primary"
                @click="dialog2 = false"
                >
                  готово
              </v-btn>
            </v-card>
          </div>
          
        <!-- <div v-show="standart"> -->
          
        <!-- </div> -->
        </v-dialog>

      </template>

      <!-- настройки сервера -->
      <template v-if="converg">
                
        <v-dialog
          v-model="dialog1"
          transition="dialog-bottom-transition"
          width="80%"
          aria-hidden="true"
        >
          <template v-slot:activator="{ props1 }">
            <v-btn
              class="mt-2 mt-5"
              width="100%"
              color="primary"
              v-bind="props1"
              @click="getConverg"
              ><v-icon>mdi-cog</v-icon>
              Настройки сервера
            </v-btn>
          </template>

          
          <!-- сами параметры через v-for -->
          <div class="module_bg">
            <v-card
              outlined
              class="pa-5"
            >
              <h1>Другие параметры</h1>
              <v-text-field
                flat
                type="number"
                required
                outlined
                clearable
                :rules="rule"
                hide-details="auto"
                v-model.number="replica"
                class="mt-5"
              >
                <template v-slot:label>
                  <toolbarInfo
                    title="Коэффициент репликации"
                    desc = "Количество реплик внутри базы данных"
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
                v-model.number="stability_coef"
                class="mt-5"
              >
                <template v-slot:label>
                  <toolbarInfo
                    title="Коэффициент стабильности (%)"
                    desc = "сколько нужно процентов запасного места (не меньше нуля)"
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
                v-model.number="VM_count"
                class="mt-5"
              >
                <template v-slot:label>
                  <toolbarInfo
                    title="число виртуальных машин"
                    desc = "число виртуальных машин, требуемых для работы всех приложений системы (не меньше нуля)"
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
                v-model.number="volume_per_VM"
                class="mt-5"
              >
                <template v-slot:label>
                  <toolbarInfo
                    title="объём одной виртуальной машины (TiB)"
                    desc = "объём требуемый для поддержания работы одной виртуальной машины. Берётся для всех виртуальных машин одинаковое (не меньше нуля)"
                  />
                </template> 
              </v-text-field>


              <h1 class="mt-5">Дополнительные сервера (ПО и ОЗУ для их работы)</h1>
              

              <div 
                v-for="(server, index) in convServParam"
                :key="index"
                class="mt-5 inCardTab"
              >
                
                
                <v-text-field
                  v-model="server.title"
                  required
                  outlined
                  clearable
                  label="название"
                  prepend-icon="mdi-table-column"
                  placeholder="hello"
                  :rules="rule"
                  hide-details="auto"
                  class="mt-2"
                >
                  <template v-slot:label>
                    <toolbarInfo
                      title="название"
                      desc = "название сервера или комплекта серверов внутри гиперконвергентной системы, которые требуют дискового пространства."
                    />
                  </template>
                </v-text-field>
                
                <v-text-field
                  v-model="server.count"
                  type="number"
                  required
                  outlined
                  clearable
                  prepend-icon="mdi-counter"
                  label="количество (шт)"
                  :rules="rule"
                  hide-details="auto"
                  class="mt-5"
                >
                  <template v-slot:label>
                    <toolbarInfo
                      title="количество (шт)"
                      desc = "количество указанных серверов внутри гиперконвергентной системы."
                    />
                  </template>
                </v-text-field>
                
                <v-text-field
                  v-model="server.volume"
                  type="number"
                  required
                  outlined
                  clearable
                  prepend-icon="mdi-disc-player"
                  label="объём ОЗУ (ГБ)"
                  :rules="rule"
                  hide-details="auto"
                  class="mt-5"
                >
                  <template v-slot:label>
                    <toolbarInfo
                      title="объём ОЗУ (ГБ)"
                      desc = "Объём оперативной памяти, требуемый для работы указанного сервера. Измеряется в Гигабайтах."
                    />
                  </template> 
                </v-text-field>
                
                <v-btn
                  color="error"
                  @click="deleteItem(server, index)"
                  class="mt-5 mx-2 mb-2"
                >  
                  <v-icon>mdi-trash-can</v-icon>  удалить 
                </v-btn>

              </div>

              <v-btn
                color="primary"
                @click="extendConvServParamList"
                class="ma-5"
              >добавить</v-btn>

              <!-- <v-btn
                color="success"
                @click="sumItUp"
                class="mt-5"
              >суммарный объём</v-btn> -->
          
            </v-card>
          </div>
        </v-dialog>
      </template>

      
      
      

      <!-- выбор режима -->
      <v-select
        required
        v-model="options.value"
        density="comfortable"
        style="margin-top: 10px"
        hide-details
        outlined
        :items="options"
        item-text="name"
        item-value="value"
        label="Выберите режим"
        class="mt-5"
      />

      <!-- ручная установка битрейта -->
      <v-text-field
        ref="mBRRef"
        required
        type="number"
        style="margin-bottom: 20px"
        v-show="options.value == 'user'"
        flat
        outlined
        clearable
        label="Мбит/с"
        :rules="rule"
        hide-details="auto"
        v-model.number="mBR_input"
        class="mt-5"
      >
        <template v-slot:label>
          <toolbarInfo
            title="Битрейт (Мбит/с)"
            desc = "Объём передаваемой информации на выходе устройства в единицу времени. Исчисляется в мегабит на секунду."
          />
        </template> 
      </v-text-field>


      <!-- доступ к вычислениям видеонаблюдения -->
      <template v-if="options.value == 'video'">
        <v-dialog
          v-model="dialog"
          novalidate
          width="90%"
          aria-hidden="true"
          class="mt-5"
        >
          <template v-slot:activator="{ props }">
            <v-btn
              width="100%"
              color="primary"
              v-bind="props"
              @click="getVideo"
              class="mt-5 mr-5"
              ><v-icon>mdi-cog</v-icon>
              настройки видеонаблюдения
            </v-btn>
          </template>

          <video1 @cam_bitrate="getMbrVideo" />
          <video_revers_vue/>

        </v-dialog>
      </template>



      <!-- старт -->
      <v-btn 
        @click="start" 
        color="primary" 
        class="mt-5"
        width="100%"
      >Старт</v-btn>
    </v-card>


    <!-- результаты вычислений -->
    <v-card 
      class="pa-5 mt-5"
      v-show="started"
    >

        <p v-if="answer.nodes"> число узлов в системе {{ answer.nodes }} шт.</p>
        <p v-if="answer.volume_TiB"> объём системы в тебибайтах {{ answer.volume_TiB }} TiB;</p>
        <p v-if="answer.volume_TB"> объём системы в терабайтах {{ answer.volume_TB }} ТБ;</p>
        

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
import video1 from "./video1.vue";
import video_revers_vue from "./video2.vue";
import headerTab from "./ui/commonUi/header.vue";
import toolbarInfo from './ui/commonUi/tooltip.vue';
export default {
  name: "shd_vue",
  components: {
    video1,
    video_revers_vue,
    headerTab,
    toolbarInfo
  },
  data() {
    return {
      snackbar: false,      // окошко об ошибке
      timeout: 2500,
      errorText: 'Неверно введены данные или они отсутствуют',


      // для пункта с информацией о модуле
      componentInfo: {
        name: 'Системы хранения данных (СХД)',
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
                'Системы видеонаблюдения          - 8 мбит/с или вычисленное в соответствующем разделе;',
                'Свой режим - указываемое пользователем значение.'
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

      convServParam: [
        {id: 0, title: `сервер управления`, count: 1, volume: 32},
        {id: 1, title: `сервер архивного управления`, count: 1, volume: 32},
        {id: 2, title: `сервер видеоархива`, count: 7, volume: 32},
        {id: 3, title: `Master-сервер ситуационного видеонаблюдения`, count: 1, volume: 192},
        {id: 4, title: `Slave-сервер ситуационного видеонаблюдения`, count: 2, volume: 192},
        {id: 5, title: `модули видеоаналитики`, count: 481, volume: 1}
      ], 
      // список дополнительных серверов вносящих свои требования к объёму схд
      sumItUpAnswer: 0, //объём всех серверов вместе взятых, ГБ



      started: false,
      answer: {},

      // volume_metadata_tib:      0, // Объём метаданных требуемых для хранения
      // volume_useful_with_copy:  0, // Полезная ёмкость с учетом резерва
      // volume_with_copy:         0, // Объём с резервным копированием
      // volume_TB:                0, // Требуемый объём СХД с учётом перевода TiB в Тбайт
      // volume_mib:               0,
      // volume_tib:               0, // Требуемый объём для хранения данных
      mBR_in_calcs:             0, // Параметр битрета для непосредственного расчёта(используется исключительно в формуле)

      daily_metatdata_GiB:      2, // среднесуточный объём метаданных для хранения видео
      
      replica:                  3, // число реплик в базе данных
      stability_coef:           20, // коэффициент запаса места для стабильной работы РСХД
      VM_count:                 14, // число виртуальных машин
      volume_per_VM:            3, // объём требуемый для одной ВМ
      volume_osvm:              0,
      volume_with_archive:      0,


      
      strange:                  37, // доселе неизвестно откуда взявшееся число

      mBR_input: "...", //Параметр битрейта для текстового поля (для передачи данных из V-model при ручном режиме или при получении битрейта от видеонаблюдения)
      wats: "", //Мощность, требуемая для рассчёта времени работы на резервном питании
      users: "...", // Число устройств
      usli: 0, //Кол-во узлов
      days: "...", //Дни для рассчёта
      disc_capacity:   8, //Объём дисков в узлах
      disc_count:      15, //Кол-во дисков в узлах
      standart_discs: false, //Параметр, отвечающий за стандартные/нестандартные узлы(по умолчанию стандартные узлы)
      converg: false, //Параметр, отвечающий за гиперконвергентную/негиперконвергентную систему(по умолчанию негиперконвергентная)
      dialog: false, //Параметр, отвечающий за отображение видеонаблюдения
      dialog1: false,
      dialog2: false,
      video: false, //Параметр, отвечающий за использование битрейта с видеонаблюдения
      rule: [(value) => !!value || "Необходимо заполнить это поле."], //Правила для текстовых полей
      options: [
        { name: "Локальная вычислительная сеть", value: "local" },
        { name: "Системы телефонии", value: "phone" },
        { name: "Система видеонаблюдения", value: "video" },
        { name: "Ручной режим", value: "user" },
      ], //Выбор режимов

      convergChecked: false,
    };
  },
  methods: {
    
    // изменение списка дополнительных серверов для гиперконвергента
    extendConvServParamList(){
      // console.log('hello, making a new member')
      // console.log(this.convServParam)
      // console.log(this.convServParam.length)
      // console.log(this.convServParam[this.convServParam.length-1])
      this.convServParam.push({
        id: this.convServParam.length, 
        title: `type idk`, 
        count: 1, 
        volume: 0,
      }) 
      
      // console.log(this.convServParam)
    },
    deleteItem: function (item, index) {
      if(this.convServParam[index] === item) { 
      // The template passes index as the second parameter to avoid indexOf, 
      // it will be better for the performance especially for one large array
      // (because indexOf actually loop the array to do the match)
        this.convServParam.splice(index, 1)
        // console.log(this.convServParam)
      } else {
        let found = this.convServParam.indexOf(item)
        this.convServParam.splice(found, 1)
      }
    },
    
    sumItUp(){
      this.sumItUpAnswer = 0
      this.convServParam.forEach( servParam => {
        this.sumItUpAnswer += (servParam.count*servParam.volume)
      })
      console.log( `${this.sumItUpAnswer} ГБ      ${this.sumItUpAnswer/1024} ТБ`)
      return this.sumItUpAnswer
    }, // суммарный объём всех серверов в гиперконвергентной вкладке
        
    getMbrVideo() {
      // console.log("cams_Mbr", (this.mBr = localStorage.getItem("Bitrate")));
      this.video = true;
      return (this.mBR_input = localStorage.getItem("Bitrate"));
    }, //Ф-ция, отвечающая за присвоение битрейта от видеокамер

    getVideo() {
      if (this.dialog === false) {
        this.dialog = true;
        this.convergChecked = true
      } else {
        this.dialog = false;
      }
    }, //Ф-ция, отвечающая за вкладку с видеонаблюдением

    getConverg(){
        if (this.dialog1 === false) {
        this.dialog1 = true;
        this.convergChecked = true
      } else {
        this.dialog1 = false;
      }
    },

    getStores(){
        if (this.dialog2 === false) {
        this.dialog2 = true;
        this.standart_discs = true
      } else {
        this.dialog2 = false;
      }
    },

    
    
    start() {
      this.started = true;
      this.getMbr();

      
      
      if(!this.converg) {
        
        if (this.standart_discs) 
          this.answer = this.standart_system(
          this.mBR_in_calcs,
          this.users,
          this.days,
          15,
          8
          );
        else  
          this.answer = this.standart_system(
          this.mBR_in_calcs,
          this.users,
          this.days,
          this.disc_count,
          this.disc_capacity
          );
        console.log("calculated standart system")
      }
      if( this.converg)  {
        
        if (this.standart_discs)  
          this.answer = this.converg_system(
            this.mBR_in_calcs,
            this.users,
            this.days,
            4.69, 
            8,
            this.VM_count,
            this.volume_per_VM
          );
        else  
          this.answer = this.converg_system(
            this.mBR_in_calcs,
            this.users,
            this.days,
            this.disc_count,
            this.disc_capacity,
            this.VM_count,
            this.volume_per_VM
          );
        
        console.log("calculated converg system")
      }


    }, 


    getMbr() {
      this.mBR_in_calcs = 0;
      if (this.options.value == "local") {
        //Локальные вычислительные сети
        this.mBR_in_calcs = 5;
      }
      if (this.options.value == "phone") {
        //Системы телефонии
        this.mBR_in_calcs = 0.1;
      }
      if (this.options.value == "video") {
        //Система видеонаблюденя
        if (this.video) {
          // если мы считали в разделе видеонаблюдения
          this.mBR_in_calcs = this.mBR_input;
        } else {
          this.mBR_in_calcs = 8;
        }
      }
      if (this.options.value == "user") {
        //Ручной режим
        this.mBR_in_calcs = this.mBR_input;
      }
    }, //Ф-ция, присваивающая значение битрейта при разных режимах



    /***
     * Функция расчета стандартных систем
     * 
     * @param {float}   mbr                 - 
     * @param {integer} users               -
     * @param {integer} days                -
     * @param {integer} node_discs          -
     * @param {float}   node_disc_capacity  -
     * 
     * @returns {object} answer
     * я хочу переделать это под систему, которая сама получая ответ будет рисовать ответ
     */
    standart_system( 
      mbr,
      users,
      days, 
      node_discs,
      node_disc_capacity,
      
    ) {
      /*
      Глобально, что здесь происходит. 
      
        Сначала считается объём в целом в  Mib
        Далее происходит перевод в TiB

        Затем ветвление, на основании режима
        Если режим видеонаблюдения, то к объёму прибавляется объём метаданных на обработку видео
        Если любой другой, то этот шаг пропускается.

        Далее 

      
      */

      var answer = {}

      answer.volume_mib = mbr * users * days * 3600 * 24 / 8;

      answer.volume_TiB = Math.ceil(answer.volume_mib / (1024*1024));


      if (this.options.value == "video"){
        answer.volume_metadata_tib = this.daily_metatdata_GiB * days * users / 1024;
        answer.volume_TiB += Math.ceil(100* answer.volume_metadata_tib)/100
      }
      answer.volume_TB = Math.ceil(
        this.TiB_to_TB(
          answer.volume_TiB
        ));
      
      // ---------------------------------------------------------------------------------------
      answer.volume_TB_with_reserve = answer.volume_TB + (this.strange * node_disc_capacity)
      // console.log("объём, вместе с резервом N+1, Tбайт:   ", answer.volume_TB_with_reserve);
      // вызывает вопросы то, что я обозначил за strange
      // ---------------------------------------------------------------------------------------
      
      answer.nodes = Math.ceil(answer.volume_TB_with_reserve / node_disc_capacity / node_discs);
      
      console.log(answer)
      return answer
    }, 










    //Функция расчета гиперконвергентных систем
    converg_system(
      mbr,
      users,
      days, 
      node_discs,
      node_disc_capacity,
      VM_count,
      volume_per_VM

    ){
      /*
      Что же происходит здесь...



        Считается объём от ОДНОГО источника за сутки в MiB
        Далее перевод в TiB

        Затем ветвление, на основании режима
        Если режим видеонаблюдения, то к объёму прибавляется объём метаданных на обработку видео
          Также происходит домножение на все коэффициенты и запасы
        Если любой другой, то этот шаг пропускается.

        Считается объём нужный на все виртуальные машины.
        Этот объём суммируется с объёмом хранения и делится на размер узла.
          Если узел стандартный, то он сильно меньше, чем у дисковых, а также он в TiB...

          Я наверное прямо перед этим сделаю перевод в терабайты
      
      */ 
      var answer = {}


      // здесь буду считать по ядрам
      // мне тут нужно количество ядер в одном ПО
      answer.nodes_cores = 0


      // здесь посчитаю по ОЗУ
      // тут нужно иметь объёмы серверов
      answer.server_volume_GB = this.sumItUp()
      answer.nodes_server_volume = Math.ceil( answer.server_volume_GB/(node_discs * node_disc_capacity) );


      
      
      answer.volume_mib = mbr * 3600 * 24 / 8 
      // console.log("volume", answer.volume_mib, "MiB");
      answer.volume_tib = answer.volume_mib / (1024*1024);
      // console.log("volume", answer.volume_tib, "TiB");

      answer.volume_with_archive = answer.volume_tib

      if (this.options.value == "video"){
        answer.volume_video = Math.ceil(100*
          (answer.volume_tib + (this.daily_metatdata_GiB/1024) )
          * users * days * this.replica * (1 + this.stability_coef/100) / 0.85)/100

        // console.log("объём кластера для видеоданных ", answer.volume_video, "TiB")
        answer.volume_osvm = Math.ceil(VM_count * volume_per_VM);
        
        
        
        answer.volume_with_archive = Math.ceil(answer.volume_video + answer.volume_osvm);
        answer.volume_TB = Math.ceil(this.TiB_to_TB(answer.volume_with_archive));
      }

      answer.nodes_volume = Math.ceil( this.TiB_to_TB( answer.volume_TB / (node_discs * node_disc_capacity)))
      

      answer.nodes = Math.max(answer.nodes_volume, answer.nodes_server_volume, answer.nodes_cores)
      console.log(answer)
      return answer
    },


    Converg() {
        let server_volume = 0
        let disc_group = 0
        if(this.convergChecked){

        server_volume = this.sumItUp()/1024
        disc_group = Math.ceil(server_volume*2 / 0.85);
        this.volume_with_copy = Math.ceil(disc_group+2*this.volume_TB)

        this.volume_useful_with_copy= Math.ceil(2*this.volume_TB + disc_group)/0.8
        }
        else{
            this.volume_with_copy= Math.ceil(this.volume_TB*2)
            console.log('Объём с резервным копированием   ', this.volume_with_copy)
            this.volume_useful_with_copy = Math.ceil(this.volume_with_copy/0.7)
            console.log('учетом резерва требуемая от СХД полезная ёмкость', this.volume_useful_with_copy)
        }
    }, //Ф-ция, учитывающая объём СХД с резервом (при гиперконвергентной системе)



    Standart() {
      if (!this.standart_discs){
        // стандартные узлы
        if (this.converg){
          // гиперконвергентные системы
          this.usli = Math.ceil(this.volume_useful_with_copy / 7 / 4);
        } else {
          // обычные системы
          this.usli = Math.ceil(this.volume_TB / 15 / 8);
        }
      } else{
        // свои узлы
        if (this.converg){ 
          // гиперконвергентные системы
          this.usli = Math.ceil(this.volume_useful_with_copy / this.disc_count / this.disc_capacity);
        } else {
          // здесь не важно, какой тип системы, ведь оно учитывалось в объёме
          this.usli = Math.ceil(this.volume_TB / this.disc_count / this.disc_capacity);
        }

      }
      console.log(this.usli);
      console.log(!!this.usli);
      if (!this.usli){
        this.snackbar = true
        this.started = false
        return
      }
      localStorage.setItem("usli", this.usli);
      localStorage.setItem("usli", this.usli + 2);
      this.$emit("Usli", this.usli);
      this.Power()
      // console.log('emitting Usli')
      

    }, //Ф-ция, рассчитывающа число узлов для различных типов систем и узлов
    

    // перевод из тебибайтов в терабайты
    TiB_to_TB(TiB_value){
      return ( TiB_value / 0.85 / 0.9094947011773 )
    },







    Power() {
      if (this.converg) {
        //Мощность при гиперконвергентной системе
        this.wats = (this.usli + 2) * 1000;
        // console.log(this.wats);
        localStorage.setItem("wats", null);
        localStorage.setItem("wats", this.wats);
        this.$emit("Power", this.wats);
      } else {
        //Мощность при негиперконвергентной системе
        this.wats = (this.usli + 2) * 700;
        // console.log(this.wats);
        localStorage.setItem("wats", null);
        localStorage.setItem("wats", this.wats);
        this.$emit("Power", this.wats);
      }
    }, //Ф-ция, передающая параметр мощности в зависимости от типа системы

  
  },
};
</script>
