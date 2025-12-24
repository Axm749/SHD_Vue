<template>
	<div>        <!-- eslint-disable -->
		<!-- заголовок -->
		<div class="header">
			<headerTab
				title="Расчет кабельных лотков"
				v-bind:info="componentInfo"
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
								desc="название кабеля."
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
								desc="количество кабелей данного вида."
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
								desc="Диаметр одного кабеля данного вида."
							/>
						</template>
					</v-text-field>

					<v-btn
						color="error"
						@click="deleteItem(server, index)"
						class="mt-5 mx-auto mb-2"
						width="100%"
					>
						<v-icon>mdi-trash-can</v-icon>
						удалить
					</v-btn>
				</div>
			</div>

			<v-row class="mt-1">
				<v-col>
					<v-btn
						color=""
						@click="cable_reset"
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
					>добавить
					</v-btn>
				</v-col>
				<!-- <v-col>
					<v-btn
						color=""
						@click="cable_reset"
						width="100%"
					>
						<v-icon>mdi-cog</v-icon>
						Дополнительные настройки
					</v-btn>
				</v-col> -->


			</v-row>


			<v-text-field
				v-model="default_height"
				type="number"
				required
				outlined
				clearable
				prepend-icon="mdi-diameter-outline"
				label="начальная высота лотка (мм)"
				:rules="rule"
				hide-details="auto"
				class="mt-5"
			>
				<template v-slot:label>
					<toolbarInfo
						title="начальная высота лотка (мм)"
						desc="высота, ниже которой лоток не предлагается."
					/>
				</template>
			</v-text-field>

			<v-row>
				<v-col cols="100%">
					<v-btn
						@click="start"
						color="primary"
						class="mt-5"
						width="100%"
					>Старт</v-btn>
				</v-col>
				<v-col cols="auto">
					<v-menu
						top
						offset-y
						:close-on-content-click="false"
						v-model="menu"
					>

						<template v-slot:activator="{ on, attrs }">
							<v-icon
								class="mt-6"
								v-bind="attrs"
								v-on="on"
							>mdi-cog
							</v-icon>
						</template>
						<v-card>
							<v-list>
								<v-list-item>
									<v-list-item-content>
										<v-list-item-title>Запас расстояния между кабелями</v-list-item-title>
									</v-list-item-content>
									<v-list-item-action>
										<v-text-field
											outlined
											clearable
											label="в миллиметрах"
											v-model.number="gap_reserve"
											type="number"
										/>
									</v-list-item-action>
								</v-list-item>
								<v-list-item>
									<v-list-item-content>
										<v-list-item-title>Запас расстояния высоты кабельного лотка</v-list-item-title>
									</v-list-item-content>
									<v-list-item-action>
										<v-text-field
											outlined
											clearable
											label="в миллиметрах"
											v-model.number="height_gap"
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
<!--			<v-btn-->
<!--				@click="secondary"-->
<!--				color="primary"-->
<!--				class="mt-5"-->
<!--				width="100%"-->
<!--			>второй старт-->
<!--			</v-btn>-->

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
			v-if="started"
		>
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
					<!-- Высота кабельного лотка -->
					<tr>
						<td>Высота кабельного лотка</td>
						<td>
							<v-chip
								:color="'grey'"
								dark
							><toolbarInfo
								:title="answer.height + '  мм'"
								:desc = "`Высота кабельного лотка с учётом заданного запаса в ${ height_gap } мм`"
							/>
							</v-chip>
						</td>
					</tr>
					<!-- Ширина кабельного лотка -->
					<tr>
						<td>Ширина кабельного лотка</td>
						<td>
							<v-chip
								:color="'grey'"
								dark
							><toolbarInfo
								:title="answer.length + '  мм'"
								:desc = "`Ширина кабельного лотка с учётом заданного запаса в ${gap_reserve} мм между каждым пучком кабелей, общий запас составляет ${gap_reserve * (answer.cables_used.length - 1)} мм`"
							/>
							</v-chip>
						</td>
					</tr>
					<!-- Площадь кабельного лотка -->
					<tr>
						<td>Площадь кабельного лотка</td>
						<td>
							<v-chip
								:color="getColor()"
								dark
							><toolbarInfo
								:title="answer.S + '  мм^2'"
								:desc = "`Площадь кабельного лотка, до применения запасов по длине и ширине.${(answer.S == answer.perfectS ? ` Рассчитанная площадь лотка (${answer.S} мм^2) равна идеальной площади лотка (${answer.perfectS} мм^2) до применения запасов.`:` Рассчитанная площадь лотка (${answer.S} мм^2) на ${Math.round(answer.percentOfPerfect - 100)}% больше, чем идеальная площадь лотка (${answer.perfectS} мм^2) до применения запасов`)}`"
							/>
							</v-chip>
						</td>
					</tr>
					</tbody>
				</template>
			</v-simple-table>
<!--			<p>{{ answer }}</p>-->
			<!--	  <td>Длина кабельного лотка: {{answer.Lotok_Shirota}} мм.</td>-->
			<!--	  <td> Высота кабельного лотка: {{answer.Lotok_Visota}} мм</td>-->

			<v-btn
				@click="started=false"
				class="mt-2"
			>скрыть
			</v-btn>
		</v-card>


	</div>
</template>

<script>
import headerTab from './ui/commonUi/header.vue';
import toolbarInfo from './ui/commonUi/tooltip.vue';
/* eslint-disable */
export default {
	name: 'lotok_vue',
	components: {
		headerTab,
		toolbarInfo
	},
	data: () => ({
		cableParam: [
			{id: 0, title: `Широкий`, count: 1, diameter: 15},
			{id: 1, title: `Тонкий`, count: 3, diameter: 10},
			{id: 2, title: `Много средних`, count: 6, diameter: 5}
		],
		defaultCableParam: [
			{id: 0, title: `Широкий`, count: 1, diameter: 15},
			{id: 1, title: `Тонкий`, count: 3, diameter: 10},
			{id: 2, title: `Много средних`, count: 6, diameter: 5}
		],
		LotokDefault: [
			{lenght: 500, height: 30},
			{lenght: 550, height: 80},
			{lenght: 600, height: 100},
			{lenght: 650, height: 150},
			{lenght: 700, height: 200},
			{lenght: 750, height: 250},
			{lenght: 800, height: 300},
		],
		answer: {
			Lotok_Visota: 0,
			Lotok_Shirota: 0
		},
		menu: false, // для работы попаппа?
		gap_reserve: 5, 	//запас между кабелями в миллиметрах
		height_gap: 5, //запас высоты в миллиметрах
		default_height: 50,	//в миллиметрах
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
			description: `В этом разделе программы рассчитываются габариты лотка, необходимого для прокладки указанных кабелей. В ходе работы алгоритма кабели с большим размером располагаются снизу, а кабели меньшего размера располагаются поверх больших кабелей. После полученного укомплектованного набора кабелей, между ними добавляется запас для монтажа и только после этого происходит вывод информации о ширине кабельного лотка (в режиме прямоугольного лотка, режим трубы в разработке). Высота кабельного лотка берёт заданное значение высоты кабеля, или же размер минимального кабеля в заданных входных параметрах. К этому значению добавляется запас высоты для крышки кабельного лотка, после чего полученное значение высоты кабельного лотка выводится в ответе.`,
			disclaimers: [
				'Модуль рассчитывает в режиме прямоугольного лотка: ',
				'В прямоугольном режиме, параметры лотка будут выданны в виде ширины и высоты с учётом необходимых запасов для кабелей и крышки кабельного лотка.',
				'Запасное расстояние между кабелями необходимо для монтажа кабелей.',
				'Запасное расстояние для крышки кабельного лотка необходимо для упрощения монтажа кабелей'

			],
		},

	}),
	methods: {
		// методы для расчёта кабельных лотков
		start() {
			this.started = true
			var max_height = Number(this.default_height)
			var length = 0
			var perfectS = 0
			this.answer.Lotok_Visota = 0
			this.answer.Lotok_Shirota = 0
			// Простой счёт лотка
			this.cableParam.forEach(item => {
				// console.log(item)
				// считаю идеальную площадь (хоть и подразумеваем как квадрат)
				perfectS += (item.diameter * item.diameter) * item.count
				console.log(perfectS, "perfectS... added", item.diameter * item.diameter * item.count, "bc item.diameter = ", item.diameter)

				if (max_height < item.diameter) {
					max_height = Number(item.diameter)
				}
				length = length + (Number(item.diameter * item.count) + (this.gap_reserve * Number(item.count)))
			})
			console.log(max_height, "max_height")
			// Оптимизация - можем ли мы наложить кабели друг на друга не увеличивая max_height?
			// Массив для оптимизации
			let Optimization_Arr_1 = []
			// Счетчик id
			var id_counter = 0
			// Создание массива с учётом формы записи кабелей из фронта (т.е. учет параметра count)
			this.cableParam.forEach(item => {
				for (var i = 0; i < item.count; i++) {
					Optimization_Arr_1.push(
						{
							id: id_counter,
							cable_width: Number(item.diameter),
							cable_height: Number(item.diameter),
							cables_used: [{
								id: id_counter,
								cable_width: Number(item.diameter),
								cable_height: Number(item.diameter),
							}]
						}
					)
					id_counter += 1
				}
			})
			// Небольшой костыль, сортирует от большего к меньшему для улучшеной работы алгоритма
			Optimization_Arr_1.sort((a, b) => b.cable_height - a.cable_height)
			console.log(Optimization_Arr_1, 'sorted')
			// Массив использованных кабелей
			let used_cables = []
			// Параметр для определения какую ширину взять, по сути костыль?
			let more_wider = 0
			let less_wider = 0
			let buffer_obj = {}
			console.log(`Result: `, Optimization_Arr_1)
			// Оптимизация, проверяется каждый с каждым, не учитывая самого себя и кабели которые уже были использованы, после чего записывает результат сложения в Optimization_arr_1
			Optimization_Arr_1.forEach(Loop_1 => {
				Optimization_Arr_1.forEach(Loop_2 => {
					// console.log(Loop_1,' ', Loop_2)
					if ((Loop_1.cable_height + Loop_2.cable_height <= Number(max_height))
						&& (Loop_1 !== Loop_2)
						&& (!used_cables.includes(Loop_1))
						&& (!used_cables.includes(Loop_2))) {
						// выбор используемой ширины
						if (Loop_1.cable_width > Loop_2.cable_width) {
							more_wider = Number(Loop_1.cable_width)
							less_wider = Number(Loop_2.cable_width)
						} else {
							more_wider = Number(Loop_2.cable_width)
							less_wider = Number(Loop_1.cable_width)
						}
						console.log(Loop_2, Loop_1)
						buffer_obj = {
							id: id_counter,
							cable_width: more_wider,
							cable_height: Number(Loop_1.cable_height + Loop_2.cable_height),
							// использованные кабели для суммирования данного стака
							cables_used: [],
							// не использованное пространство
							unspace_width: (more_wider - less_wider),
							unspace_height: less_wider
						}
						// заполнение пустого пространства
						Optimization_Arr_1.forEach(Loop_3 => {
							if ((Loop_3.cable_height <= buffer_obj.unspace_height)
								&& (Loop_3.cable_width <= buffer_obj.unspace_width)
								&& (Loop_3 !== buffer_obj)
								&& (Loop_3 !== Loop_2)
								&& (Loop_3 !== Loop_1)
								&& (!used_cables.includes(Loop_3))) {
								// Уменьшаем неиспользованное пространство
								buffer_obj.unspace_width = buffer_obj.unspace_width - Loop_3.cable_width
								// Добавляем кабели для заполнения в стак в массив использованных кабелей
								Loop_3.cables_used.forEach(put_used_cables => {
									buffer_obj.cables_used.push(put_used_cables)
								})
								used_cables.push(Loop_3)
							}
						})
						// добавление использованных кабелей для создания стака
						Loop_1.cables_used.forEach(put_used_cables => {
							buffer_obj.cables_used.push(put_used_cables)
						})
						Loop_2.cables_used.forEach(put_used_cables => {
							buffer_obj.cables_used.push(put_used_cables)
						})
						// записываем созданый объект в массив оптимизации
						Optimization_Arr_1.push(buffer_obj)
						// записываем в использованные кабели, нужно для *удаления* использованных ПРОМЕЖУТОЧНЫХ элементов
						used_cables.push(Loop_1)
						used_cables.push(Loop_2)
						id_counter += 1
						// Optimization_Arr_1.splice(Optimization_Arr_1.indexOf(Loop_1),1)
						// Optimization_Arr_1.splice(Optimization_Arr_1.indexOf(Loop_2),1)
					}
				})
			})
			// удаляем использованные кабели из результата
			used_cables.forEach(ToDelete => {
				Optimization_Arr_1.splice(Optimization_Arr_1.indexOf(ToDelete), 1)
			})

			// this.Optimization_Arr_1.forEach(item => {
			// 	for (var i = 0; i<item.count; i++) {
			// 		this.answer.push(
			// 			{
			// 				id: id_counter,
			// 				cable_width: Number(item.diameter),
			// 				cable_height: Number(item.diameter),
			// 				cables_used: [{
			// 					id: id_counter,
			// 					cable_width: Number(item.diameter),
			// 					cable_height: Number(item.diameter),
			// 				}]
			// 			}
			// 		)
			// 		id_counter += 1
			// 	}
			// })

			var max_length_end = 0
			var max_height_end = Number(this.default_height)
			Optimization_Arr_1.forEach(cable => {
				if (cable["cable_height"] >= max_height_end) {
					max_height_end = cable["cable_height"]
				}
				max_length_end += cable["cable_width"]
			})


			this.answer = {

				S: max_length_end * max_height_end,
				perfectS: perfectS,
				percentOfPerfect: (max_length_end * max_height_end / perfectS * 100),
				percentOfNonOptimised: ((max_length_end * max_height_end) / (max_height * length) * 100),
				length: length + (this.gap_reserve * (Optimization_Arr_1.length - 1)),
				height: Number(max_height) + (this.height_gap),
				SNonOptimised: length * max_height,
				cables_used: Optimization_Arr_1
			}


			console.log(Optimization_Arr_1, 'Optimization_Arr_1 result')

		},              //Ф-ция старт


		// альтернативная версия алгоритма подсчета лотков
		/**
		 * @return answer {
		 *     width: float,
		 *     height: float,
		 *     S: float,
		 *     cable_list: [{}]
		 * }
		 */
		secondary() {
			this.started = true
			var max_height = 0
			var length = 0
			// Простой счёт лотка (все в шеренгу)
			this.cableParam.forEach(item => {
				if (max_height < item.diameter) {
					max_height = item.diameter
				}
				length = length + (Number(item.diameter * item.count) + (this.gap_reserve * Number(item.count)))
			})
			console.log(max_height, 'max height')
			console.log(length, 'length')

			// Массив для оптимизации
			let main_arr = []
			// Счетчик id
			var id_counter = 0
			// Создание массива с учётом формы записи кабелей из фронта (т.е. учет параметра count)
			this.cableParam.forEach(item => {
				for (let i = 0; i < item.count; i++) {
					main_arr.push(
						{
							id: id_counter,
							cable_width: Number(item.diameter),
							cable_height: Number(item.diameter),
							unused_width: 0,
							unused_height: 0,
							cables_used: [{
								id: id_counter,
								cable_width: Number(item.diameter),
								cable_height: Number(item.diameter)
							}],

						}
					)
					id_counter += 1
				}
			})
			console.log(main_arr, 'main arr')

			var j_forbidden = [],
				new_obj = {},
				new_arr = [],
				possible_S = 0,
				current_S = 0


			// Оптимизация - можем ли мы наложить кабели друг на друга
			for (let i = 0; i < main_arr.length; i++) {
				// обнуляем важные переменные
				j_forbidden = []
				// console.log(i, 'i')

				// ВАЖНО! Я здесь начинаю второй цикл не с нуля, а со значения первого цикла,
				// чтобы более мелкие объекты не смотрели на более крупные, что уже позади.
				// Ведь эта работа бессмысленна
				for (let j = i; j < main_arr.length; j++) {
					console.log(i, 'i', j, 'j')
					// обнуляем служебные переменные
					new_obj = {}
					new_arr = []
					possible_S = 0

					if (j_forbidden.includes(j)) {
						console.log(j, 'is in', j_forbidden, 'must skip')
					}
					if (i === j) {
						console.log('Dont stack a cable with himself')
					}
					// 	i - кого совмещаем _ main_arr[i]
					// 	j - с кем совмещаем _ main_arr[j]

					// объединяем два кабеля друг на друга при одном условии:
					// - площадь лотка становится меньше или остаётся неизменной (possible_S <= current_S)
					// для проведения подсчетов предполагаем, что изменение уже произошло
					new_obj = this.make_new_arr(main_arr, i, j);
					new_arr = new_obj.new_arr;

					// потенциальная площадь
					possible_S = new_obj.new_S;

					// текущая площадь
					current_S = this.make_S(main_arr)

					if (possible_S < current_S) {
						console.log('hello', possible_S <= current_S)
						console.log('hello', possible_S, current_S)
						main_arr = new_arr
						// 	также нужно указать, что индекс j в таком случае использовать нельзя
						j_forbidden.push(j)
						console.log('j_forbidden', j_forbidden)
					} else {
						console.log("didn't stack items:", main_arr[i], main_arr[j])
					}
				}
			}


		},
		// совместить два кабеля и составить новый массив из них
		/**
		 *
		 * @param main_arr
		 * @param add_to_id
		 * @param add_id
		 *
		 * @return {
		 *          new_arr: [],
		 *          max_height: float,
		 *          total_width: float,
		 *          new_S: float
		 *          }
		 */
		make_new_arr(new_arr, add_to_id, add_id) {
			console.log(new_arr, add_to_id, add_id)
			// здесь и лежит главная логика объединения двух элементов
			// мы имеем лишь два объекта: new_arr[add_to_id] и new_arr[add_id]
			if (
				(new_arr[add_to_id] != new_arr[add_id])
				&& (1)
			) {
				console.log("можно объединить кабели: ",)
				new_arr[add_to_id] = {}
				// и обнуляем исходный объект
				new_arr[add_id] = {}
			}


			return {
				new_arr: new_arr,
				max_height: 1,
				total_width: 2,
				new_S: this.make_S(new_arr)
			}
		},


		deleteItem: function (item, index) {
			if (this.cableParam[index] === item) {
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
		cable_reset: function () {
			this.cableParam = [
				{id: 0, title: `Широкий`, count: 1, diameter: 15},
				{id: 1, title: `Несколько средних`, count: 3, diameter: 10},
				{id: 2, title: `Много тонких`, count: 6, diameter: 5}
			]
			// По сути это костыль, лучше как-то отменить мутацию defaultcableParam от изменения cableParam
			// console.log("Hiihihihihihiii!!!11")
		},

		// расчет площади для массива кабелей
		make_S(main_arr) {
			var current_width = 0
			main_arr.forEach(item => {
				current_width += item.cable_width
			})
			console.log(current_width, 'current width')

			var current_height = 0
			main_arr.forEach(item => {
				if (current_height < item.cable_height) {
					current_height = item.cable_height
				}
			})
			console.log(current_height, 'current height')

			var current_S = current_width * current_height;
			console.log(current_S, 'current S')
			return current_S;
		},

		// методы для графики
		// получение цвета для рендера ответа
		getColor(){
			if( this.answer.percentOfPerfect <= 105 ){
				return 'green'
			} else if ( this.answer.percentOfPerfect <= 150 ){
				return 'yellow'
			} else if ( this.answer.percentOfPerfect <= 200 ){
				return 'orange'
			}
			return 'red'
		}
	},
	mounted() {
		console.log('hello')
	}
}
</script>

<style>
.inLine {
	display: inline-block;
}
</style>
