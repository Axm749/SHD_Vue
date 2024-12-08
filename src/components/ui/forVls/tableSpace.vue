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
				
                :src="$vuetify.theme.dark ? require('../commonUi/images/theme/autumnBg.jpg') : require('../commonUi/images/theme/winterBg2.jpg')"
				></v-img>
			</template>

			<v-app-bar-nav-icon
            @click="notice"
            ></v-app-bar-nav-icon>

			<v-app-bar-title> Выбор таблицы </v-app-bar-title>

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
                max-height="70%"
			>
				<v-tabs-items v-model="tab" style="margin-top: 226px;">	
					<v-tab-item>
						<cableTable 
                            v-bind:data="DPT"
                            v-bind:name="items[0]"
                            @getVlsSelected="VLS_selected"
                        />
					</v-tab-item>

					<v-tab-item>
						<cableTable 
                            v-bind:data="DPTS"
                            v-bind:name="items[1]"
                            @getVlsSelected="VLS_selected"
                        />
					</v-tab-item>

					<v-tab-item>
						<cableTable 
                            v-bind:data="DOTA"
                            v-bind:name="items[2]"
                            @getVlsSelected="VLS_selected"
                        />
					</v-tab-item>
					
					<v-tab-item>
						<cableTable 
                            v-bind:data="DOTS"
                            @getVlsSelected="VLS_selected"
                        />
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
                        Mark: `ДПТ до 48ОВ`,
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
                        Mark: "ДПТ до 48ОВ",
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
                        Mark: "ДПТ до 48ОВ",
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
                    {
                        Mark: "ДПТ до 48ОВ",
                        MDRN: 8,
                        MRN: 2.0,
                        MPR: 10.82,
                        weight: 118.0,
                        diameter: 12.3,
                        Slice: 118.1,
                        L_nach: 5.73,
                        L_Kon: 6.19,
                        L_feat: 4.01,
                        TKLR: 12.65,
                    },
                    {
                        Mark: "ДПТ до 48ОВ",
                        MDRN: 10,
                        MRN: 2.5,
                        MPR: 13.36,
                        weight: 120.6,
                        diameter: 12.4,
                        Slice: 120.1,
                        L_nach: 6.96,
                        L_Kon: 7.52,
                        L_feat: 4.87,
                        TKLR: 9.72,
                    },
                    {
                        Mark: "ДПТ до 48ОВ",
                        MDRN: 12,
                        MRN: 3.0,
                        MPR: 16.1,
                        weight: 123.4,
                        diameter: 12.5,
                        Slice: 123.4,
                        L_nach: 8.24,
                        L_Kon: 8.90,
                        L_feat: 5.77,
                        TKLR: 7.60,
                    },
                    {
                        Mark: "ДПТ до 48ОВ",
                        MDRN: 15,
                        MRN: 3.75,
                        MPR: 20.4,
                        weight: 127.8,
                        diameter: 12.6,
                        Slice: 125.4,
                        L_nach: 10.17,
                        L_Kon: 10.98,
                        L_feat: 7.12,
                        TKLR: 5.42,
                    },
                    {
                        Mark: "ДПТ до 48ОВ",
                        MDRN: 20,
                        MRN: 5.0,
                        MPR: 27.06,
                        weight: 136.2,
                        diameter: 13.2,
                        Slice: 137.0,
                        L_nach: 12.35,
                        L_Kon: 13.34,
                        L_feat: 8.65,
                        TKLR: 3.47,
                    },
                    {
                        Mark: "ДПТ до 48ОВ",
                        MDRN: 25,
                        MRN: 6.25,
                        MPR: 33.71,
                        weight: 143.3,
                        diameter: 13.5,
                        Slice: 143.0,
                        L_nach: 14.65,
                        L_Kon: 15.82,
                        L_feat: 10.26,
                        TKLR: 2.26,
                    },
                    {
                        Mark: "ДПТ до 48ОВ",
                        MDRN: 30,
                        MRN: 7.5,
                        MPR: 40.35,
                        weight: 150.34,
                        diameter: 13.8,
                        Slice: 150.0,
                        L_nach: 16.74,
                        L_Kon: 18.08,
                        L_feat: 11.72,
                        TKLR: 1.44,
                    }

                ],
            },
            {
                name: "ДПТ до 72ОВ",
                content: [
                    { 
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 4, 
                        MRN: 1.0, 
                        MPR: 9.55, 
                        weight: 126.4, 
                        diameter: 12.8, 
                        Slice: 128.1, 
                        L_nach: 4.66, 
                        L_Kon: 5.03, 
                        L_feat: 3.26, 
                        TKLR: 16.35 
                    }, 
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 6,
                        MRN: 1.5,
                        MPR: 9.55,
                        weight: 126.4,
                        diameter: 12.8,
                        Slice: 128.1,
                        L_nach: 4.66, 
                        L_Kon: 5.03, 
                        L_feat: 3.26, 
                        TKLR: 16.35 
                    },
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 7,
                        MRN: 1.75,
                        MPR: 9.55,
                        weight: 126.4,
                        diameter: 12.8,
                        Slice: 128.1,
                        L_nach: 4.66, 
                        L_Kon: 5.03, 
                        L_feat: 3.26, 
                        TKLR: 16.35 
                    },
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 8,
                        MRN: 2.0,
                        MPR: 10.14,
                        weight: 127.0,
                        diameter: 12.8,
                        Slice: 128.6,
                        L_nach: 4.93, 
                        L_Kon: 5.32, 
                        L_feat: 3.45, 
                        TKLR: 15.24 
                    },
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 10,
                        MRN: 2.5,
                        MPR: 12.88,
                        weight: 129.8,
                        diameter: 12.9,
                        Slice: 130.6,
                        L_nach: 6.16, 
                        L_Kon: 6.65, 
                        L_feat: 4.31, 
                        TKLR: 11.41 
                    },
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 12,
                        MRN: 3.0,
                        MPR: 15.22,
                        weight: 132.2,
                        diameter: 13.0,
                        Slice: 132.4,
                        L_nach: 7.19, 
                        L_Kon: 7.77, 
                        L_feat: 5.03, 
                        TKLR: 9.23 
                    },
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 15,
                        MRN: 3.75,
                        MPR: 19.13,
                        weight: 136.1,
                        diameter: 13.1,
                        Slice: 135.4,
                        L_nach: 8.83, 
                        L_Kon: 9.54, 
                        L_feat: 6.18, 
                        TKLR: 6.77 
                    },
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 20,
                        MRN: 5.0,
                        MPR: 25.4,
                        weight: 143.8,
                        diameter: 13.6,
                        Slice: 145.8,
                        L_nach: 10.89, 
                        L_Kon: 11.76, 
                        L_feat: 7.62, 
                        TKLR: 4.48 
                    },
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 25,
                        MRN: 6.25,
                        MPR: 32.04,
                        weight: 150.9,
                        diameter: 13.9,
                        Slice: 152.6,
                        L_nach: 13.13, 
                        L_Kon: 14.18, 
                        L_feat: 9.19, 
                        TKLR: 2.99 
                    },
                    {
                        Mark: "ДПТ до 72ОВ",
                        MDRN: 30,
                        MRN: 7.5,
                        MPR: 38.30,
                        weight: 157.5,
                        diameter: 14.2,
                        Slice: 158.9,
                        L_nach: 15.06, 
                        L_Kon: 16.26, 
                        L_feat: 10.54, 
                        TKLR: 2.06 
                    }
                        // Здесь по тому же образцу

                ],
            },
            {
                name: "ДПТ до 96ОВ",
                content: [
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 4, 
                        MRN: 1.0, 
                        MPR: 11.2, 
                        weight: 137.8, 
                        diameter: 13.4, 
                        Slice: 140.4, 
                        L_nach: 4.99, 
                        L_Kon: 5.39, 
                        L_feat: 3.49, 
                        TKLR: 15.32 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 6, 
                        MRN: 1.5, 
                        MPR: 11.2, 
                        weight: 137.8, 
                        diameter: 13.4, 
                        Slice: 140.4, 
                        L_nach: 4.99, 
                        L_Kon: 5.39, 
                        L_feat: 3.49, 
                        TKLR: 15.32 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 7, 
                        MRN: 1.75, 
                        MPR: 11.2, 
                        weight: 137.3, 
                        diameter: 13.4, 
                        Slice: 140.4, 
                        L_nach: 4.99, 
                        L_Kon: 5.39, 
                        L_feat: 3.49, 
                        TKLR: 15.32 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 8, 
                        MRN: 2.0, 
                        MPR: 11.2, 
                        weight: 137.8, 
                        diameter: 13.4, 
                        Slice: 140.4, 
                        L_nach: 4.99, 
                        L_Kon: 5.39, 
                        L_feat: 3.49, 
                        TKLR: 15.32 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 10, 
                        MRN: 2.5, 
                        MPR: 12.18, 
                        weight: 138.8, 
                        diameter: 13.4, 
                        Slice: 141.1, 
                        L_nach: 5.39, 
                        L_Kon: 5.82, 
                        L_feat: 3.77, 
                        TKLR: 13.87 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 12, 
                        MRN: 3.0, 
                        MPR: 14.72, 
                        weight: 141.3, 
                        diameter: 13.5, 
                        Slice: 143.0, 
                        L_nach: 6.43, 
                        L_Kon: 6.94, 
                        L_feat: 4.50, 
                        TKLR: 10.99 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 15, 
                        MRN: 3.75, 
                        MPR: 18.24, 
                        weight: 144.8, 
                        diameter: 13.6, 
                        Slice: 145.6, 
                        L_nach: 7.83, 
                        L_Kon: 8.46, 
                        L_feat: 5.48, 
                        TKLR: 8.34 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 20, 
                        MRN: 5.0, 
                        MPR: 24.49, 
                        weight: 151.1, 
                        diameter: 13.8, 
                        Slice: 150.2, 
                        L_nach: 10.19, 
                        L_Kon: 11.01, 
                        L_feat: 7.13, 
                        TKLR: 5.49 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 25, 
                        MRN: 6.25, 
                        MPR: 30.76, 
                        weight: 158.8, 
                        diameter: 14.3, 
                        Slice: 161.6, 
                        L_nach: 11.89, 
                        L_Kon: 12.84, 
                        L_feat: 8.32, 
                        TKLR: 3.87 
                    },
                    { 
                        Mark: "ДПТ до 96ОВ",
                        MDRN: 30, 
                        MRN: 7.5, 
                        MPR: 36.62, 
                        weight: 165.0, 
                        diameter: 14.6, 
                        Slice: 167.5, 
                        L_nach: 13.66, 
                        L_Kon: 14.75, 
                        L_feat: 9.56, 
                        TKLR: 2.81 
                    }
                        // Здесь по тому же образцу

                ],
            },
            {
                name: "ДПТ до 144ОВ",
                content: [
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 4, 
                        MRN: 1.0, 
                        MPR: 11.67, 
                        weight: 148.4, 
                        diameter: 14.0, 
                        Slice: 153.3, 
                        L_nach: 4.76, 
                        L_Kon: 5.14, 
                        L_feat: 3.33, 
                        TKLR: 15.70 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 6, 
                        MRN: 1.5, 
                        MPR: 11.67, 
                        weight: 148.4, 
                        diameter: 14.0, 
                        Slice: 153.3, 
                        L_nach: 4.76, 
                        L_Kon: 5.14, 
                        L_feat: 3.33, 
                        TKLR: 15.70 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 7, 
                        MRN: 1.75, 
                        MPR: 11.67, 
                        weight: 148.4, 
                        diameter: 14.0, 
                        Slice: 153.3, 
                        L_nach: 4.76, 
                        L_Kon: 5.14, 
                        L_feat: 3.33, 
                        TKLR: 15.70 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 8, 
                        MRN: 2.0, 
                        MPR: 12.25, 
                        weight: 148.9, 
                        diameter: 14.0, 
                        Slice: 153.8, 
                        L_nach: 4.98, 
                        L_Kon: 5.38, 
                        L_feat: 3.49, 
                        TKLR: 14.81 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 10, 
                        MRN: 2.5, 
                        MPR: 15.58, 
                        weight: 152.2, 
                        diameter: 14.1, 
                        Slice: 156.2, 
                        L_nach: 6.23, 
                        L_Kon: 6.73, 
                        L_feat: 4.36, 
                        TKLR: 11.06 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 12, 
                        MRN: 3.0, 
                        MPR: 18.31, 
                        weight: 155.0, 
                        diameter: 14.2, 
                        Slice: 158.2, 
                        L_nach: 7.23, 
                        L_Kon: 7.81, 
                        L_feat: 5.06, 
                        TKLR: 8.99 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 15, 
                        MRN: 3.75, 
                        MPR: 23.0, 
                        weight: 159.6, 
                        diameter: 14.3, 
                        Slice: 161.6, 
                        L_nach: 8.89, 
                        L_Kon: 9.6, 
                        L_feat: 6.22, 
                        TKLR: 6.58 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 20, 
                        MRN: 5.0, 
                        MPR: 30.44, 
                        weight: 168.4, 
                        diameter: 14.9, 
                        Slice: 173.6, 
                        L_nach: 10.96, 
                        L_Kon: 11.84, 
                        L_feat: 7.67, 
                        TKLR: 4.34 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 25, 
                        MRN: 6.25, 
                        MPR: 38.26, 
                        weight: 176.5, 
                        diameter: 15.2, 
                        Slice: 181.4, 
                        L_nach: 13.18, 
                        L_Kon: 14.23, 
                        L_feat: 9.23, 
                        TKLR: 2.9 
                    },
                    { 
                        Mark: "ДПТ до 144ОВ",
                        MDRN: 30, 
                        MRN: 7.5, 
                        MPR: 45.69, 
                        weight: 184.6, 
                        diameter: 15.6, 
                        Slice: 191.0, 
                        L_nach: 14.95, 
                        L_Kon: 16.15, 
                        L_feat: 10.47, 
                        TKLR: 1.99 
                    }
                        // Здесь по тому же образцу

                ],
            }
        ],
        DPTS: [
            {
                name: "ДПТС до 48ОВ",
                content: [
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 4,
                        MRN: 1.0,
                        MPR: '-',
                        weight: 121.8,
                        diameter: 12.5,
                        Slice: 122.6,
                        L_nach: 3.86,
                        L_Kon: 4.17,
                        L_feat: 2.70,
                        TKLR: 23.09,
                    },
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 6,
                        MRN: 1.5,
                        MPR: '-',
                        weight: 123.2,
                        diameter: 12.5,
                        Slice: 123.6,
                        L_nach: 4.10,
                        L_Kon: 4.43,
                        L_feat: 2.87,
                        TKLR: 21.97,
                    },
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 7,
                        MRN: 1.75,
                        MPR: '-',
                        weight: 127.3,
                        diameter: 12.7,
                        Slice: 126.6,
                        L_nach: 4.79,
                        L_Kon: 5.17,
                        L_feat: 3.35,
                        TKLR: 19.33,
                    },
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 8,
                        MRN: 2.0,
                        MPR: '-',
                        weight: 130.1,
                        diameter: 12.8,
                        Slice: 128.5,
                        L_nach: 5.24,
                        L_Kon: 5.66,
                        L_feat: 3.67,
                        TKLR: 18.0,
                    },
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 10,
                        MRN: 2.5,
                        MPR: '-',
                        weight: 137.5,
                        diameter: 13.1,
                        Slice: 134.0,
                        L_nach: 6.27,
                        L_Kon: 6.77,
                        L_feat: 4.39,
                        TKLR: 15.62,
                    },
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 12,
                        MRN: 3.0,
                        MPR: '-',
                        weight: 145.1,
                        diameter: 13.3,
                        Slice: 139.5,
                        L_nach: 7.21,
                        L_Kon: 7.79,
                        L_feat: 5.05,
                        TKLR: 14.02,
                    },
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 15,
                        MRN: 3.75,
                        MPR: '-',
                        weight: 157.2,
                        diameter: 13.9,
                        Slice: 151.0,
                        L_nach: 8.61,
                        L_Kon: 9.3,
                        L_feat: 6.03,
                        TKLR: 11.96,
                    }
                ],
            },
        ],
        DOTA: [
        ], 
        DOTS: [
        {
                name: "ДПТС до 48ОВ",
                content: [
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 4,
                        MRN: 1.0,
                        MPR: '-',
                        weight: 121.8,
                        diameter: 12.5,
                        Slice: 122.6,
                        L_nach: 3.86,
                        L_Kon: 4.17,
                        L_feat: 2.70,
                        TKLR: 23.09,
                    },
                    {
                        Mark: "ДПТС до 48ОВ",
                        MDRN: 6,
                        MRN: 1.5,
                        MPR: '-',
                        weight: 123.2,
                        diameter: 12.5,
                        Slice: 123.6,
                        L_nach: 4.10,
                        L_Kon: 4.43,
                        L_feat: 2.87,
                        TKLR: 21.97,
                    }
                ],
            },
        ],
        


        
    }),
    methods: {
        VLS_selected(data){
            this.selected = data
            // console.log('selected is writing', this.selected)
            this.$emit('anotherGetVlsSelected', this.selected)
        },
        notice(){
            console.log('ну привет, человек')
        }
    },
};
</script>

