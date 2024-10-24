<!-- элемент для внесения сюда всех таблиц для выбора параметров кабеля -->
<template>
    <div>
    
        <cableTable v-bind:data="DPT"/>
        <cableTable v-bind:data="DPTS"/>
        <cableTable v-bind:data="DOTS"/>
        <cableTable v-bind:data="DOTA"/>

            
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

        started: false, //Параметр, отвечающий за вывод результатов работы программы при нажатии "Старт"
        analog: false, //Параметр, отвечающий за вывод результатов работы программы при аналоговом расчёте
        rule: [(value) => !!value || "Необходимо заполнить это поле."], //Правила для текстовых полей
        accept: false,
        search: "",

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
        DPTS: [],
        DOTA: [], 
        DOTS: [],
        


        
    }),
    methods: {
        
    },
    };
</script>

