<!-- элемент для выбора климатической зоны -->
<!-- элемент для внесения сюда всех таблиц для выбора параметров кабеля -->
<template>
    <div>
		
		<v-card class="overflow-hidden">
			<v-app-bar
			absolute
			dark
			shrink-on-scroll
			prominent
			fade-img-on-scroll
			scroll-target="#scrolling-techniques-3"
			>
				<template v-slot:img="{ props }">
					<v-img
					v-bind="props"
					src="../commonUi/images/autumnBg.jpg"
					></v-img>
				</template>

				<v-btn icon>
					<v-icon>mdi-content-save</v-icon>
				</v-btn>

				<v-app-bar-title>
					Выбор климата
				</v-app-bar-title>

			</v-app-bar>


			<v-sheet
			id="scrolling-techniques-3"
			class="overflow-y-auto"
			max-height="600px"
			>
				<v-card height="800px" style="margin-top: 126px;">
					
					<v-card-title primary-title>
						сделать отображение получше, как и найти пикчу получше
					</v-card-title>
					<v-img src="./images/climateIceMap.jpg"></v-img>
					<v-img src="./images/climateWindMap.gif"></v-img>
					<v-card-text>
						Если совсем будем с жиру беситься, 
						то можем сделать интерактивную карту, 
						как на сайте 
						<a target="_blank" href="https://vols.expert/configurator/interactive-climate-maps/">Волс.Эксперт</a>
					</v-card-text>


				</v-card>
			</v-sheet>
		</v-card>       
    </div>
</template>

<script>
// import cableTable from './cableTable.vue';

export default {
    name: "tableSpace",
    // components: {
    //     cableTable,
    // },
    data: () => ({
        // в этом модуле будет храниться вся база климатических зон

        started: false, //Параметр, отвечающий за вывод результатов работы программы при нажатии "Старт"
        analog: false, //Параметр, отвечающий за вывод результатов работы программы при аналоговом расчёте
        rule: [(value) => !!value || "Необходимо заполнить это поле."], //Правила для текстовых полей
        accept: false,
        search: "",

        // что выбрано пользователем
        selected: [],

        // база данных климатических зон
		headers:[

		],
		data: [
			{
				Field: '1',
				d: 10,
			},
			{
				Field: '2',
				d: 15,
			},
			{
				Field: '3',
				d: 20,
			},
			{
				Field: '4',
				d: 25,
			},
			{
				Field: '5',
				d: 30,
			},
			{
				Field: '6',
				d: 35,
			},
			{
				Field: '7',
				d: 40,
			},
			{
				Field: 'SPECIAL',
				d: 50,
			},
		]
        


        
    }),
    methods: {
        
    },
    };
</script>

