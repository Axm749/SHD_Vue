<template>
    <div class="module_bg">
        <div class="header">
          <headerTab title="расчет опор линии связи" v-bind:info="componentInfo"></headerTab>
        </div>

		<v-card class="overflow-hidden">
			
			<v-img :src="require(`../images/climateIceMap.jpg`)"></v-img>
			<br>
				<v-data-table
				v-bind:headers="headers"
				v-bind:items="item"
				hide-default-footer
				@click:row="onClickRow"
				>
				</v-data-table>
				<br>
				<br>

		</v-card>
			
            
    </div>
</template>

<script>
import headerTab from '@/components/ui/commonUi/header.vue';

    export default{
    name: "IceZoneTable",
	components: {
		headerTab
	},
    props:{
        data:Array
    },
    data: () => ({
        search: "",
        selected: [],
        headers: [
        { text: "Район по гололёду", value: "Icearea",  align: "left" },
        { text: "Нормативная толщина стенки гололёда bэ, мм", value: "iceWidth"},
        ],
        item: [
			{
				Icearea: '1',
				iceWidth: 10,
			},
			{
				Icearea: '2',
				iceWidth: 15,
			},
			{
				Icearea: '3',
				iceWidth: 20,
			},
			{
				Icearea: '4',
				iceWidth: 25,
			},
			{
				Icearea: '5',
				iceWidth: 30,
			},
			{
				Icearea: '6',
				iceWidth: 35,
			},
			{
				Icearea: '7',
				iceWidth: 40,
			},
			{
				Icearea: 'SPECIAL',
				iceWidth: 50,
			},
		],

		componentInfo: {
			name: 'выбор зоны по гололеду',
			incomes: [
				'выбранная зона'
				],
			outcomes: [
			'толщина льда для рассчетов и номер этой зоны',
			], 
			description: 'для ознакомления представлена карта РФ с обозначениями по соответствующим стандартам',
			disclaimers: [
			'дисклеймер'
			]
		}
    }),
    methods: {
        async onClickRow(item) {
			this.selected = [{
				Icearea: item.Icearea,
				iceWidth: item.iceWidth,
			}]
            console.log('selected: ',this.selected)
            this.$emit('getIceZoneSelected', this.selected)
        }
    },
}

</script>

<style>

</style>