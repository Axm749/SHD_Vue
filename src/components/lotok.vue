<template>
  <div>
    <!-- заголовок -->
    <div class="header">
        <headerTab 
            title="Расчет кабельных лотков"
            v-bind:info = "componentInfo"
        ></headerTab>
    </div>
    
    <!-- поля ввода -->
    <v-card class="pa-5" tile>

        <h2 class="my-5">Параметры кабеля</h2>

        <div 
            v-for="(server, index) in cableParam"
            :key="index"
            class="inLine"
        >   
            <div class="inCardTab mr-1 mt-1">
                    <v-text-field
                        v-model="server.title"
                        required
                        outlined
                        clearable
                        label="название"
                        prepend-icon="mdi-rename-outline"
                        placeholder="hello"
                        :rules="rule"
                        hide-details="auto"
                        class="mt-2"
                    >
                        <template v-slot:label>
                        <toolbarInfo
                            title="название"
                            desc = "название кабеля."
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
                            desc = "количество кабелей данного вида."
                        />
                        </template>
                    </v-text-field>
                    
                    <v-text-field
                        v-model="server.diameter"
                        type="number"
                        required
                        outlined
                        clearable
                        prepend-icon="mdi-diameter-outline"
                        label="Диаметр (мм)"
                        :rules="rule"
                        hide-details="auto"
                        class="mt-5"
                    >
                        <template v-slot:label>
                        <toolbarInfo
                            title="Диаметр (мм)"
                            desc = "Диаметр одного кабеля данного вида."
                        />
                        </template> 
                    </v-text-field>
                    
                    <v-btn
                        color="error"
                        @click="deleteItem(server, index)"
                        class="mt-5 mx-auto mb-2"
                        width="100%"
                    >  
                        <v-icon>mdi-trash-can</v-icon>  удалить 
                    </v-btn>
                </div>
            

        </div>

        <v-row class="mt-1">
            <v-col>
                <v-btn
                    color=""
                    @click="cabel_reset"
                    width="100%"
                >
                    Сбросить все кабели
                </v-btn>
            </v-col>
            <v-col>
                <v-btn
                    color="primary"
                    @click="cableParam.push(
                        {id: 0, title: `новый`, count: 1, diameter: 1}
                    )"
                    width="100%"
                >добавить</v-btn>
            </v-col>
            <v-col>
                <v-btn
                    color=""
                    @click="cabel_reset"
                    width="100%"
                >
                    <v-icon>mdi-cog</v-icon>
                    Дополнительные настройки
                </v-btn>
            </v-col>
            

            
        </v-row>

        






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

       <p>{{answer}}</p>
<!--	  <td>Длина кабельного лотка: {{answer.Lotok_Shirota}} мм.</td>-->
<!--	  <td> Высота кабельного лотка: {{answer.Lotok_Visota}} мм</td>-->
      
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
/* eslint-disable no-use-before-define */
export default{
    name: 'lotok_vue',
    components: {
        headerTab,
        toolbarInfo
        },
    data: () =>({
        cableParam: [
            {id: 0, title: `Много тонкий`, count: 5, diameter: 2},
            {id: 1, title: `Много средних`, count: 5, diameter: 4}
        ],
        defaultCableParam: [
            {id: 0, title: `Широкий`, count: 1, diameter: 15},
            {id: 1, title: `Тонкий`, count: 1, diameter: 5},
            {id: 2, title: `Много средних`, count: 7, diameter: 7}
        ],
        LotokDefault: [
            {lenght: 500,height: 30},
            {lenght: 550,height: 80},
            {lenght: 600,height: 100},
            {lenght: 650,height: 150},
            {lenght: 700,height: 200},
            {lenght: 750,height: 250},
            {lenght: 800,height: 300},
        ],
        answer: {
			Lotok_Visota: 0,
            Lotok_Shirota: 0
        },
        gap_reserve: 5, //в миллиметрах
        started: false,  //Параметр, отвечающий за вывод результатов работы электропитания после нажатия "Старт"
        rule: [
            value => !!value || 'Необходимо заполнить это поле.',
        ],                       //Правила для текстовых полей
        snackbar: false,      // окошко об ошибке
        timeout: 2500,
        errorText: 'Неверно введены данные или они отсутствуют',


        // для пункта с информацией о модуле
        componentInfo: {
            name: 'расчет кабельных лотков',
            incomes: [
            'Название кабелей;', 
            'Диаметры кабелей (мм);', 
            'Кол-во кабелей одного вида (шт.);', 
            'запас диаметра для прокладки (мм).',
            ],
            outcomes: [
            'Размеры лотка, необходимые для прокладки кабеля;'], 
            description: `В этом разделе программы рассчитываются габариты лотка, необходимого для прокладки указанных кабелей.`,
            disclaimers: [
            'Модуль рассчитывает в двух режимах работы: ',
            'В прямоугольном, лоток будет параметры ширины и высоты;',
            'В круговом, труба имеет значение радиуса.',
            "_",
            
            ],
            constants: [
            {
                name: 'PlaceHolder 1 value', 
                massive: [ 
                    'PlaceHolder 1 value'
                ]
            },
            {
                name: 'PlaceHolder 2 value', 
                massive: [ 
                    'PlaceHolder 2 value'
                ]
            },
            ]
        },

    }),
	methods:{
		start(){
			this.started = true
			var max_height = 0
			var length = 0
			this.answer.Lotok_Visota = 0
			this.answer.Lotok_Shirota = 0
			// Простой счёт лотка
			this.cableParam.forEach(item => {
				if (max_height < item.diameter) {
					max_height = item.diameter
				}
				length = length + (Number(item.diameter * item.count) + (this.gap_reserve * Number(item.count)))
			})
			// Оптимизация - можем ли мы наложить кабели друг на друга не увеличивая max_height?
			// Массив для оптимизации
			let Optimization_Arr_1 = []
			// Счетчик id
			var id_counter = 0
			// Создание массива с учётом формы записи кабелей из фронта (т.е. учет параметра count)
			this.cableParam.forEach(item => {
				for (var i = 0; i<item.count; i++) {
					Optimization_Arr_1.push(
						{
							id: id_counter,
							cabel_width: Number(item.diameter),
							cabel_height: Number(item.diameter)
						}
					)
					id_counter += 1
				}
			})
			// Небольшой костыль, сортирует от большего к меньшему для улучшеной работы алгоритма
			Optimization_Arr_1.sort((a, b) => b.cabel_height - a.cabel_height)
			console.log(Optimization_Arr_1,'sorted')
			// Массив использованных кабелей
			let used_cabels = []
			// Параметр для определения какую ширину взять, по сути костыль?
			let which_wider = 0
			console.log(`Result: `,Optimization_Arr_1)
			// Оптимизация, проверяется каждый с каждым, не учитывая самого себя и кабели которые уже были использованы, после чего записывает результат сложения в Optimization_arr_1
			Optimization_Arr_1.forEach(Loop_1 => {
				Optimization_Arr_1.forEach(Loop_2 => {
					if (Loop_1.cabel_height + Loop_2.cabel_height <= max_height && Loop_1 != Loop_2 && !used_cabels.includes(Loop_1) && !used_cabels.includes(Loop_2)) {
						// выбор используемой ширины
						if (Loop_1.cabel_width > Loop_2.cabel_width){
							which_wider = Loop_1.cabel_width
						} else {
							which_wider = Loop_2.cabel_width
						}
                        Optimization_Arr_1.push(
							{
								id: id_counter,
								cabel_width: which_wider,
								cabel_height: Number(Loop_1.cabel_height + Loop_2.cabel_height),
								// использованные кабели для суммирования
								cabels_used: [Loop_1,Loop_2],
								// unused_volume: (which_wider*Number(Loop_1.cabel_height + Loop_2.cabel_height))- (()+())
							}
						)
						// записываем в использованные кабели
						used_cabels.push(Loop_1)
						used_cabels.push(Loop_2)
						id_counter += 1
						// Optimization_Arr_1.splice(Optimization_Arr_1.indexOf(Loop_1),1)
						// Optimization_Arr_1.splice(Optimization_Arr_1.indexOf(Loop_2),1)
					}
				})
			})
			// удаляем использованные кабели из результата
			used_cabels.forEach(ToDelete => {
				Optimization_Arr_1.splice(Optimization_Arr_1.indexOf(ToDelete),1)
			})
			this.answer = Optimization_Arr_1
			console.log(Optimization_Arr_1, 'Optimization_Arr_1 result')
			console.log(used_cabels, 'cabels used to make stacks')
			// length = length - this.gap_reserve
			// this.answer.Lotok_Visota = Number(max_height)
			// this.answer.Lotok_Shirota = Number(length)

		},              //Ф-ция старт
		deleteItem: function (item, index) {
			if(this.cableParam[index] === item) {
			// The template passes index as the second parameter to avoid indexOf,
			// it will be better for the performance especially for one large array
			// (because indexOf actually loop the array to do the match)
			this.cableParam.splice(index, 1)
			// console.log(this.convServParam)
			} else {
			let found = this.cableParam.indexOf(item)
			this.cableParam.splice(found, 1)
			}
		},
		cabel_reset: function(){
			this.cableParam = [
				{id: 0, title: `Широкий`, count: 1, diameter: 15},
				{id: 1, title: `Тонкий`, count: 1, diameter: 5},
				{id: 2, title: `Много средних`, count: 7, diameter: 7}
			]
			// По сути это костыль, лучше как-то отменить мутацию defaultcableParam от изменения cableParam
			console.log("Hiihihihihihiii!!!11")
		}


        /**
            // Box_Info.Wide_info input panel, ну короче в vuetify закинешь строчку на ввод данных для широты лотка
            //  Box_Info.Height_Info input panel, то же самое для высоты лотка
                Cabel_Laying_Scheme = [[],[]]
                // Двойной массив, по сути матрица уложения кабеля
                Box_Info = { Wide_Info: 20, Height_Info: 5 }
                // Информацая о размерах в миллиметрах !
                Cabel_Info = [ { Cabel_Name: 'Шаурма 1', Cabel_Inner_Diameter: 5 }, { Cabel_Name: 'Шаурма 2', Cabel_Inner_Diameter: 5 }, { Cabel_Name: 'Шаурма 3', Cabel_Inner_Diameter: 5 }, { Cabel_Name: 'Шаурма 465', Cabel_Inner_Diameter: 5 } ]
                // Информацая о размерах в миллиметрах ! Диаметр кабеля с учетом изоляции...? Я не оч уверен но вроде да.
                Total_row_sum = 0
                // Сумма длин диаметров для одного слоя
                Height_Modifier = 0
                // По сути выбор уровня слоя, на котором будут расположены кабели
                for (let i = 0; i<Cabel_Info.length; i++){
                    if (Box_Info.Wide_Info >= Cabel_Info[i].Cabel_Inner_Diameter + 5) {
                        if (Box_Info.Wide_Info > Total_row_sum && Box_Info.Wide_Info >= Total_row_sum + Cabel_Info[i].Cabel_Inner_Diameter + 5){
                            Total_row_sum = Total_row_sum + Cabel_Info[i].Cabel_Inner_Diameter + 5
                            Cabel_Laying_Scheme[Height_Modifier].push(Cabel_Info[i])
                            console.log(Cabel_Laying_Scheme)
                            console.log(Total_row_sum)
                        } else {
                            console.log("Can't fit more in a row!")
                            Height_Modifier = Height_Modifier + 1
                            Total_row_sum = 0
                        }
                    } else {
                        console.log("This won't fit!")
                    }
                    // if (Box_Info.Wide_Info > Cabel_Info[i].Cabel_Inner_Diameter) {
                    //     console.log("It fits")
                    // }
                }
            // !!Баг, при переключении уровней теряется кабель!! Как починить пока не знаю
         */


















    },
      mounted(){
          console.log('hello')
      }
  }
</script>

<style>
.inLine{
    display:inline-block;
}
</style>