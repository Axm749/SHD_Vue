<!-- 
 


Модуль обратного расчета битрейта.
Вынес для того, чтобы он больше не мешал



-->


<template>
    <div>

        <!-- обратный расчет -->
        <div class="module_bg mt-5">
            <div class="header">
                <headerTab 
                    title="Обратный расчет битрейта"
                    v-bind:info = "componentInfo"
                ></headerTab>
            </div>
            <v-card
                class="pa-5"
            >
                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    label="Плотность изображения (пикселей/м^2)"
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="plot"
                    class="mt-5"
                />
                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    label="Покрываемая площадь (м^2)"
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="square"
                    class="mt-5"
                />

                <v-row>
                    <v-col>
                        <v-btn 
                            @click="addZone" 
                            color="primary" 
                            class="mt-5"
                            width="100%"
                        >
                        Добавить
                        </v-btn>
                    </v-col>
                    <v-col>
                        <v-btn 
                            @click="resetZone" 
                            color="primary" 
                            class="mt-5 ml-2"
                            width="100%"
                        >
                            Сброс
                        </v-btn>
                    </v-col>
                </v-row>
                <br>
                <v-divider/>                            
                <v-row>
                    <v-col  cols="100%">
                    <p class="text-left pt-3">
                        <v-icon>
                            mdi-camera
                        </v-icon>
                        Полученный битрейт для камеры: 
                        <v-chip>
                            {{ final_mBR_visual }}
                        </v-chip> 
                    </p>
                    </v-col>
                    <v-col cols="5">
                        <v-btn
                        class="mt-3"
                        @click="analog=false"
                        v-if="analog"
                        >
                            скрыть
                        </v-btn>
                    </v-col>
                </v-row>
                <v-divider/>
                <v-simple-table 
                    fixed-header
                    height="200px"
                    v-if="analog"
                >
                    <thead>
                        <tr>
                            <th class="text-left">
                                Номер зоны
                            </th>
                            <th class="text-left">
                                число пикселей в зоне (пикселей)
                            </th>
                            <th class="text-left">
                                Вносимый битрейт (Мбит/с)
                            </th>
                            <th class="text-left">
                                Покрываемая площадь (м^2)
                            </th>
                            <th class="text-left">
                                PPM в зоне (пикселей/м^2)
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="i in PPMZones" :key="i">
                            <td> 
                                {{PPMZoneCount[i-1]}}
                            </td>
                            <td> 
                                {{pixelsCount[i-1]}}
                            </td>
                            <td> 
                                {{mBR[i-1].toFixed(3)}}
                            </td>
                            <td> 
                                {{squareInput[i-1].toFixed(3)}}
                            </td>
                            <td> 
                                {{PPMInput[i-1].toFixed(3)}}
                            </td>
                        </tr>
                    </tbody>
                </v-simple-table>

            </v-card>
        </div>
    </div>

</template>

<script>
import headerTab from "./ui/commonUi/header.vue";
// import toolbarInfo from './ui/commonUi/tooltip.vue';

export default {
    components: {
        headerTab,
        // toolbarInfo,
    },
    name: 'video_revers_vue',
    data:() =>({
        // для пункта с информацией о модуле
        componentInfo: {
            name: 'Обратный расчет битрейта видеонаблюдения, через плотность изображения и площади',
            incomes: [
            'Плотность изображения (пикселей/м^2)', 
            'Покрываемая площадь (м^2)',
            
            ],
            outcomes: [
            'Битрейт на выходе видеокамеры',
            'что-то, что я ещё не придумал'
            ], 
            description: `Обратный расчет битрейта видеонаблюдения, через плотность изображения и площади.`,
            disclaimers: [
                'не делайте значений меньше нуля'
            ]
        
        },
        plot: 10000,             //Плотность изображения
        square: 160,           //Покрываемая площадь
        kodak:0.15,             //Параметр кодака
        analog:false,           //Параметр, отвечающий за вывод результатов работы программы при аналоговом расчёте
        rule: [
            value => !!value || 'Необходимо заполнить это поле.',
        ],                              //Правила для текстовых полей


        final_mBR:0,
        result:[1],
        pixelsCountTotal:0,
        
        PPMZones: 0,
        PPMZoneCount: [],
        pixelsCount:[],
        mBR: [],

        PPMInput:[],
        squareInput: [],

        
    }),

    methods:{


        addZone(){
            this.started = false
            this.analog = true
            let FPS = 30
            this.PPMInput.push(this.plot)
            this.squareInput.push(this.square)

            this.pixelsCount.push(this.plot * this.square)
            this.mBR.push( (this.pixelsCount[this.PPMZones]* FPS*+this.kodak)/1048576 )
            this.PPMZones+=1
            this.PPMZoneCount.push(this.PPMZones)


            this.pixelsCountTotal = +this.plot * +this.square
            this.final_mBR += (this.pixelsCountTotal* FPS*+this.kodak)/1048576
            // localStorage.setItem('Bitrate', +this.final_mBR)
        },

        resetZone(){
            this.started = false
            localStorage.setItem('Bitrate', Number('0'))
            this.final_mBR = 0
            this.pixelsCountTotal = 0
            this.PPMZones = 0,
            this.PPMZoneCount = []
            this.pixelsCount = []
            this.mBR = []

            this.PPMInput = []
            this.squareInput = []
        }

    },
    computed: {
        final_mBR_visual(){
            if((this.final_mBR/1024) <= 1){
                return `${this.final_mBR.toFixed(3)} Мбит/с`
            }
            return `${(this.final_mBR/1024).toFixed(3)} Гбит/с`
        }
    },

    }
</script>

<style>

</style>