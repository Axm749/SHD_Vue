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
			scroll-target="#scrolling-techniques-2"
			>
			<template v-slot:img="{ props }">
				<v-img
				v-bind="props"
                
				src="../commonUi/images/autumnBg.jpg"
				></v-img>
			</template>

			<v-app-bar-nav-icon></v-app-bar-nav-icon>

			<v-app-bar-title>Выбор таблицы</v-app-bar-title>

			<template v-slot:extension>
				<v-tabs v-model="tab">
				<v-tab
					v-for="item in items" 
					:key="item"
				>{{item}}</v-tab>
				</v-tabs>
			</template>
			</v-app-bar>


			<v-sheet
			id="scrolling-techniques-2"
			class="overflow-y-auto"
			max-height="600px"
			>
				<v-tabs-items v-model="tab" style="height: 1000px; margin-top: 226px;">	
					<v-tab-item>
						<cableTable v-bind:data="DPT"/>
					</v-tab-item>

					<v-tab-item>
						<cableTable v-bind:data="DPTS"/>
					</v-tab-item>

					<v-tab-item>
						<cableTable v-bind:data="DOTS"/>
					</v-tab-item>
					
					<v-tab-item>
						<cableTable v-bind:data="DOTA"/>
					</v-tab-item>
					
				</v-tabs-items>
			
			</v-sheet>
		</v-card>              
    </div>
</template>

<script>
import cableTable from './cableTable.vue';

export default {
    name: "tableSpace",
    components: {
        cableTable,
    },
    data: () => ({
        // в этом модуле будет храниться вся база данных марок кабелей

        tab: null,
		items: [
          'ДПТ', 'ДПТс', 'ДОТа', 'ДОТс',
        ],

        // что выбрано пользователем
        selected: [],

        // база данных марок
        // Здесь Макс называл переменные, я пока не тронул
        DPT: [
            // Здесь я мог бы и иначе уточнить выбор, 
            // но оно сделано массивом объектов чисто для 
            // построения таблиц директивой v-for
            {
                name: "ДПТ до 48ОВ",
                content: [
                    {
                        Mark: "ДПТ до 48ОВ (6x8)",
                        MDRN: 4,
                        MRN: 1.0,
                        MPR: 8.48,
                        weight: 115.6,
                        diameter: 12.2,
                        Slice: 116.3,
                        L_nach: 4.56,
                        L_Kon: 4.92,
                        L_feat: 3.19,
                        TKLR: 16.92,
                    },
                    {
                        Mark: "",
                        MDRN: 6,
                        MRN: 1.5,
                        MPR: 8.48,
                        weight: 115.6,
                        diameter: 12.2,
                        Slice: 116.3,
                        L_nach: 4.56,
                        L_Kon: 4.92,
                        L_feat: 3.19,
                        TKLR: 16.92,
                    },
                    {
                        Mark: "",
                        MDRN: 7,
                        MRN: 1.75,
                        MPR: 9.45,
                        weight: 116.6,
                        diameter: 12.2,
                        Slice: 117.0,
                        L_nach: 5.05,
                        L_Kon: 5.45,
                        L_feat: 3.54,
                        TKLR: 14.89,
                    },
                        // Здесь по тому же образцу

                ],
            },
            {
                name: "ДПТ до 72ОВ",
                content: [
                    { 
                        Mark: "ДПТ до 72ОВ (6x12)",
                        MDRN: 7, 
                        MRN: 1.75, 
                        MPR: 9.45, 
                        weight: 116.6, 
                        diameter: 12.2, 
                        Slice: 117, 
                        L_nach: 5.05, 
                        L_Kon: 5.45, 
                        L_feat: 3.54, 
                        TKLR: 14.89 
                    }, 
                    {
                        Mark: "(данные от балды)",
                        MDRN: 100,
                        MRN: 1.55,
                        MPR: 9.45,
                        weight: 116.6,
                        diameter: 12.2,
                        Slice: 117.0,
                        L_nach: 5.05,
                        L_Kon: 5.45,
                        L_feat: 3.54,
                        TKLR: 14.89,
                    },
                        // Здесь по тому же образцу

                ],
            }
        ],
        DPTS: [
            {
                name: "ДПТС до 48ОВ",
                content: [
                    {
                        Mark: "ДПТС до 48ОВ (6x8)",
                        MDRN: 4,
                        MRN: 1.0,
                        MPR: 8.48,
                        weight: 115.6,
                        diameter: 12.2,
                        Slice: 116.3,
                        L_nach: 4.56,
                        L_Kon: 4.92,
                        L_feat: 3.19,
                        TKLR: 16.92,
                    },
                    {
                        Mark: "здесь всё тоже от балды",
                        MDRN: 6,
                        MRN: 1.5,
                        MPR: 8.48,
                        weight: 115.6,
                        diameter: 12.2,
                        Slice: 116.3,
                        L_nach: 4.56,
                        L_Kon: 4.92,
                        L_feat: 3.19,
                        TKLR: 16.92,
                    },
                    {
                        Mark: "",
                        MDRN: 7,
                        MRN: 1.75,
                        MPR: 9.45,
                        weight: 116.6,
                        diameter: 12.2,
                        Slice: 117.0,
                        L_nach: 5.05,
                        L_Kon: 5.45,
                        L_feat: 3.54,
                        TKLR: 14.89,
                    },
                ],
            },
        ],
        DOTA: [], 
        DOTS: [],
        


        
    }),
    methods: {
        
    },
    };
</script>

<style>
.a{
    background-color: rgba(red, green, blue, alpha);
}
</style>
