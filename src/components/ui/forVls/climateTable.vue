<!-- элемент для выбора климатической зоны -->
<!-- элемент для внесения сюда всех таблиц для выбора параметров кабеля -->
<template>
    <div>
		<v-card>
			<v-card-title primary-title>
				title
			</v-card-title>

			<v-data-table
				headers="headers"
				items="data"
				hide-actions
				item-key="Field"
				loading="true"
			>
				
			</v-data-table>

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
        // в этом модуле будет храниться вся база данных марок кабелей

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

