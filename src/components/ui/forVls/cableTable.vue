<!-- элемент для внесения сюда всех таблиц для выбора параметров кабеля -->
<template>
    <div>
    
        <v-data-table
        v-bind:headers="headers"
        v-bind:items="items"
        hide-default-footer
        
        >
            <template v-slot:header>
                <thead>
                <tr>
                    <th style="border-bottom: none">Марка</th>
                    <th colspan="3">Растягивающее усиление, кН</th>
                    <th colspan="1" style="border-bottom: none">Вес, кг/мм</th>
                    <th colspan="1" style="border-bottom: none">Диаметр, мм</th>
                    <th colspan="1" style="border-bottom: none">Сечение, мм<sup>2</sup></th>
                    <th colspan="3">Модуль упругости, кН/мм<sup>2</sup></th>
                    <th colspan="1" style="border-bottom: none">ТКЛР<sup>4</sup>, 1/°C, 10<sup>-6</sup></th>                 
                </tr>
                </thead>
            </template>
        </v-data-table>

            
    </div>
</template>

<script>


export default {
    name: "vlsTab;e",
    data: () => ({
        started: false, //Параметр, отвечающий за вывод результатов работы программы при нажатии "Старт"
        analog: false, //Параметр, отвечающий за вывод результатов работы программы при аналоговом расчёте
        rule: [(value) => !!value || "Необходимо заполнить это поле."], //Правила для текстовых полей
        accept: false,
        search: "",
        selected: [],
        headers: [
        {
            text: "",
            align: "left",
            sortable: false,
            value: "Mark",
        },
        { text: "МДРН", value: "MDRN", sortable: false },
        { text: "МРН", value: "MRN", sortable: false },
        { text: "МПР", value: "MPR", sortable: false },
        { text: "", value: "weight", sortable: false },
        { text: "", value: "diameter", sortable: false },
        { text: "", value: "Slice", sortable: false },
        { text: "Начальный", value: "L_nach", sortable: false },
        { text: "Конечный", value: "L_Kon", sortable: false },
        { text: "Вытяжки", value: "L_feat", sortable: false },
        { text: "", value: "TKLR", sortable: false },

        ],
        items: [
        {
            value: false,
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
            value: false,
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
            value: false,
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
        // {
        //   value: false,
        //   MDRN: 4,
        //   MRN: 1.0,
        //   MPR: 8.48,
        //   weight: 115.6,
        //   diameter: 12.2,
        //   Slice: 116.3,
        //   L_nach: 4.56,
        //   L_Kon: 4.92,
        //   L_feat: 3.19,
        //   TKLR: 16.92,
        // },
        // {
        //   value: false,
        //   MDRN: 4,
        //   MRN: 1.0,
        //   MPR: 8.48,
        //   weight: 115.6,
        //   diameter: 12.2,
        //   Slice: 116.3,
        //   L_nach: 4.56,
        //   L_Kon: 4.92,
        //   L_feat: 3.19,
        //   TKLR: 16.92,
        // },
        // {
        //   value: false,
        //   MDRN: 4,
        //   MRN: 1.0,
        //   MPR: 8.48,
        //   weight: 115.6,
        //   diameter: 12.2,
        //   Slice: 116.3,
        //   L_nach: 4.56,
        //   L_Kon: 4.92,
        //   L_feat: 3.19,
        //   TKLR: 16.92,
        // },
        // {
        //   value: false,
        //   MDRN: 4,
        //   MRN: 1.0,
        //   MPR: 8.48,
        //   weight: 115.6,
        //   diameter: 12.2,
        //   Slice: 116.3,
        //   L_nach: 4.56,
        //   L_Kon: 4.92,
        //   L_feat: 3.19,
        //   TKLR: 16.92,
        // },
        // {
        //   value: false,
        //   MDRN: 4,
        //   MRN: 1.0,
        //   MPR: 8.48,
        //   weight: 115.6,
        //   diameter: 12.2,
        //   Slice: 116.3,
        //   L_nach: 4.56,
        //   L_Kon: 4.92,
        //   L_feat: 3.19,
        //   TKLR: 16.92,
        // },
        // {
        //   value: false,
        //   MDRN: 4,
        //   MRN: 1.0,
        //   MPR: 8.48,
        //   weight: 115.6,
        //   diameter: 12.2,
        //   Slice: 116.3,
        //   L_nach: 4.56,
        //   L_Kon: 4.92,
        //   L_feat: 3.19,
        //   TKLR: 16.92,
        // },
        // {
        //   value: false,
        //   MDRN: 4,
        //   MRN: 1.0,
        //   MPR: 8.48,
        //   weight: 115.6,
        //   diameter: 12.2,
        //   Slice: 116.3,
        //   L_nach: 4.56,
        //   L_Kon: 4.92,
        //   L_feat: 3.19,
        //   TKLR: 16.92,
        // },
        ],
    }),
    methods: {
        
    },
    };
</script>

<style>
::-webkit-scrollbar {
width: 5px;
}

/* Track */
::-webkit-scrollbar-track {
background: #d2c8c8;
grid-template-columns: repeat(3, 1fr);
}

.mycols {
display: grid;
}

/* Handle */
::-webkit-scrollbar-thumb {
background: rgb(148, 148, 156);
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
background: #555;
}

.v-dialog {
box-shadow: none;
}
.th_with_rowspan_border{
border-bottom: none
}

</style>
