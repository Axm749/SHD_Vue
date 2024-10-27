<!-- элемент для внесения сюда всех таблиц для выбора параметров кабеля -->
<template>
    <div>
        <div v-for="item in data" :key="item.name">
            <v-data-table
            v-bind:headers="headers"
            v-bind:items="item.content"
            hide-default-footer
            @click:row="onClickRow"
            
            >
                <template v-slot:header>
                    <thead>
                    <tr>
                        <th style="border-bottom: none">Марка</th>
                        <th colspan="3">Растягивающее усиление, кН</th>
                        <th colspan="1" style="border-bottom: none">Вес, кг/км</th>
                        <th colspan="1" style="border-bottom: none">Диаметр, мм</th>
                        <th colspan="1" style="border-bottom: none">Сечение, мм<sup>2</sup></th>
                        <th colspan="3">Модуль упругости, кН/мм<sup>2</sup></th>
                        <th colspan="1" style="border-bottom: none">ТКЛР<sup>4</sup>, 1/°C, 10<sup>-6</sup></th>                 
                    </tr>
                    </thead>
                </template>
            </v-data-table>
            <br>
            <br>
        </div>
            
    </div>
</template>

<script>


export default {
    name: "cableTable",
    props:{
        data: Array,
    },
    data: () => ({
        started: false, //Параметр, отвечающий за вывод результатов работы программы при нажатии "Старт"
        analog: false, //Параметр, отвечающий за вывод результатов работы программы при аналоговом расчёте
        rule: [(value) => !!value || "Необходимо заполнить это поле."], //Правила для текстовых полей
        accept: false,
        search: "",
        selected: [],
        headers: [
        { text: "", value: "Mark", sortable: false,  align: "left" },
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




    }),
    methods: {
        async onClickRow(item) {
			this.selected = [{
				Mark: item.Mark,
				MDRN: item.MDRN,
				MRN: item.MRN,
				MPR: item.MPR,
                Weight: item.weight,
                Diameter: item.diameter,
                Slice: item.Slice,
                L_nach: item.L_nach,
                L_kon: item.L_Kon,
                L_feat: item.L_feat,
                TLKR: item.TKLR
			}]
            // console.log('selected: ',this.selected)
            this.$emit('getVlsSelected', this.selected)
        }
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

#scrolling-techniques-2 > div > div > div > div > div > div > div > table > tbody > tr > td.text-left{
    border-bottom: none;
    color: transparent;
}

#scrolling-techniques-2 > div > div > div > div > div > div > div > table > tbody > tr:first-child > td.text-left{
    color: black;
}

</style>


