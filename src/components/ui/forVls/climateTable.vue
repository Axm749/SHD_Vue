<!-- элемент для выбора климатической зоны -->
<!-- элемент для внесения сюда всех таблиц для выбора параметров кабеля -->
<template>
    <div>
		
		<v-card class="overflow-hidden">

			

			<v-app-bar
				absolute
				dark
				color="primary"
				shrink-on-scroll
				fade-img-on-scroll
				scroll-target="#scrolling-techniques-3"
			>
				<template v-slot:img="{ props }">
					<v-img
					v-bind="props"
					src="../commonUi/images/autumnBg.jpg"
					></v-img>
				</template>



				<v-app-bar-nav-icon
				></v-app-bar-nav-icon>
					<br>
					<br>
					Выбор параметров по климатическим зонам

					
				<template v-slot:extension>
					<v-tabs v-model="climate">
					<v-tab
						v-for="item in items" 
						:key="item"
					>{{item}}</v-tab>
					</v-tabs>
				</template>
			</v-app-bar>

			<v-sheet
			id="scrolling-techniques-3"
			class="overflow-y-auto"
			height="100%"
			>
				<v-tabs-items v-model="climate" style="margin-top: 226px;">	
					<v-tab-item>
						
						<IceZoneTable
							@getIceZoneSelected="vlsSelectedIce"
						/>
					</v-tab-item>

					<v-tab-item>
						
						<AirZoneTable
							@getAirZoneSelected="vlsSelectedWind"
						/>
					</v-tab-item>
					
				</v-tabs-items>

			</v-sheet>
		</v-card>       
    </div>
</template>

<script>
// import cableTable from './cableTable.vue';
import AirZoneTable from './ClimateTables/AirZoneTable.vue'
import IceZoneTable from './ClimateTables/IceZoneTable.vue';

export default {
	components:{
		AirZoneTable,
		IceZoneTable
	},
    name: "tableSpace",
    // components: {
    //     cableTable,
    // },
    data: () => ({
        // в этом модуле будет храниться вся база климатических зон
		climate: null,
		items: [
          'Гололёд', 'Ветровое давление'
        ],
        started: false, //Параметр, отвечающий за вывод результатов работы программы при нажатии "Старт"
        analog: false, //Параметр, отвечающий за вывод результатов работы программы при аналоговом расчёте
        rule: [(value) => !!value || "Необходимо заполнить это поле."], //Правила для текстовых полей
        accept: false,
        search: "",

        // что выбрано пользователем
        selected: [],
		selected_ice: [],
		selected_wind: []

        // база данных климатических зон

        
    }),
    methods: {
        vlsSelectedWind(data){
            this.selected_wind = data
            // console.log('selected is writing', this.selected)
            this.$emit('windGetVlsSelected', this.selected_wind)
        },

		vlsSelectedIce(data){
            this.selected_ice = data
            // console.log('selected is writing', this.selected)
            this.$emit('iceGetVlsSelected', this.selected_ice)
        },
    },
    };
</script>

