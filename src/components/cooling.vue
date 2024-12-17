<template>
    <div>
      <!-- заголовок -->
      <div class="header">
        <headerTab 
        title="Расчет системы кондиционирования"
        v-bind:info = "componentInfo"
        ></headerTab>
      </div>        
        <v-card class="pa-5" tile>
            <!-- <h1>Система охлаждения</h1> -->
            
            <!-- <v-checkbox 
                label="Расчёт в Вт?" 
                v-model="WattOption"
                class="mt-2"
            /> -->
            <v-switch
                v-model="WattOption"
                :label="`расчет в: ${measureUnit}`"
            ></v-switch>


            <!-- колиичество узлов -->
            <v-row class="mx-0 mb-2"> 

                <v-text-field
                :disabled="Usli_self == false "
                type="number"
                outlined
                required
                clearable
                label="Количество узлов СХД (шт)"
                :rules="rule"
                hide-details="auto"
                v-model.number="Usli_shd"
                class="mt-2"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title= "Количество узлов СХД (шт)"
                            :desc = componentInfo.incomes[0]
                        />
                    </template> 

                </v-text-field>

                <v-checkbox 
                    info
                    hide-details
                    label="Ввести вручную?" 
                    v-model="Usli_self"
                    class="mt-5 ml-2"   
                />
            </v-row>
            

            <!-- Максимальное тепловыделение -->
            <v-text-field
                type="number"
                outlined
                required
                clearable
                :rules="rule"
                hide-details="auto"
                v-model.number="Heat"
                class="mt-5"
            >
                <template v-slot:label>
                    <toolbarInfo
                        :title = heat_label
                        :desc = heat_label_description
                    />
                </template> 

            </v-text-field>
            
            <!-- усредененное тепловыделение узла -->
            <v-text-field
                type="number"
                outlined
                required
                clearable
                label="Усреднённое тепловыделение в процентах (%)"
                :rules="rule"
                hide-details="auto"
                v-model.number="K_sred"
                class="mt-5"
                >
                <template v-slot:label>
                    <toolbarInfo
                        title = "Усреднённое тепловыделение в процентах (%)"
                        :desc = componentInfo.incomes[1]
                    />
                </template> 

            </v-text-field>

            <v-text-field
                type="number"
                outlined
                required
                clearable
                :rules="rule"
                hide-details="auto"
                v-model.number="condition"
                class="mt-5"
            >
                <template v-slot:label>
                    <toolbarInfo
                        :title= condition_label
                        :desc = condition_label_description
                    />
                </template> 

            </v-text-field>

            <v-checkbox 
                label="Использовать коммутаторы видеонаблюдения?" 
                v-on:change="komutat_Opt"
                class="mt-5"
            />

            <div v-if="komutat_opt">
                <h2> Дополнительные устройства</h2>
                <!--  -->
                <v-text-field
                    type="number"
                    outlined
                    required
                    clearable
                    label="Колличество коммутаторов ядра"
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="count_yadr"
                    class="mt-5"
                    >
                    <template v-slot:label>
                        <toolbarInfo
                            title= "Колличество коммутаторов ядра"
                            :desc = componentInfo.incomes[3]
                        />
                    </template> 

                </v-text-field>
                <!--  -->
                <v-text-field
                    type="number"
                    outlined
                    required
                    clearable
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="komutat_yadr"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            :title= komutat_yadr_label
                            :desc = komutat_yadr_label_description
                        />
                    </template> 
                </v-text-field>
                <!--  -->
                <v-text-field
                    type="number"
                    outlined
                    required
                    clearable
                    label="Колличество коммутаторов доступа"
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="count_dost"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title= "Колличество коммутаторов доступа"
                            :desc = componentInfo.incomes[3]
                        />
                    </template> 

                </v-text-field>
                <!--  -->
                <v-text-field
                    type="number"
                    outlined
                    required
                    clearable
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="komutat_dost"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            :title= komutat_dost_label
                            :desc = komutat_dost_label_description
                        />
                    </template> 

                </v-text-field>
            </div>

            

            <v-btn 
                @click="start" 
                color="primary"
                class="mt-2"
                width="100%"
            >Старт</v-btn>

        </v-card>

        <!-- уведомление об ошибке -->
        <v-snackbar
            v-model="snackbar"
            :timeout="timeout"
            
        >
        {{ errorText }}

            <template v-slot:action="{ attrs }">
                <v-btn
                color="accent"
                text
                v-bind="attrs"
                @click="snackbar = false"
                >
                ОК
                </v-btn>
            </template>
        </v-snackbar>
    
    <!-- вывод ответа -->
    <v-card
        class="pa-5 mt-5"
        v-show="started"
    >
        <h2>Необходимое число кондиционеров: </h2>
        <p><strong>{{ final_result }} шт.</strong></p>
        <v-btn
          @click="started=false"
          class="mt-2"
        >скрыть</v-btn>
    </v-card>

    



    </div>
</template>

<script>
import headerTab from './ui/commonUi/header.vue';
import toolbarInfo from './ui/commonUi/tooltip.vue';
export default {
    name: 'my-cooling',
    components: {
        headerTab,
        toolbarInfo
    },
    data:() =>({
        started: false,         // вывод результатов расчета (показывать или нет)

        final_result:0,         // количество кондиционеров

        komutat_opt: false,     // опция учета дополнительных коммутаторов
        Usli_self: false,       // дать ли пользователю вводить число узлов СХД
        Usli_shd: '...',        // число узлов СХД


        snackbar: false,      // окошко об ошибке
        timeout: 2500,
        errorText: 'Неверно введены данные или они отсутствуют',


        K_sred: '...',          // Усреднённое тепловыделение в процентах (0-100)

        WattOption: true,           // использование ваттов как единиц измерения
        Heat: '...',            // тепловыделение одного узла
        komutat_yadr:'...',     // Тепловыделение коммутатора ядра
        count_yadr:'...',       // Количество коммутаторов ядра
        komutat_dost:'...',     // Тепловыделение коммутатора доступа
        count_dost:'...',       // Количество коммутаторов доступа
        condition:'...',        // Мощность кондиционера

        rule: [
            value => !!value || 'Необходимо заполнить это поле.',
            ],                  //Правила для текстовых полей
        
        // для пункта с информацией о модуле
        componentInfo: {
            name: 'Расчет системы кондиционирования',
            incomes: [
                    'Количество узлов системы хранения данных, которые нужно охлаждать. Получается автоматически из раздела расчета системы хранения данных, измеряется в штуках;', 
                    'Усреднённое тепловыделение в процентах, берётся в пределах от 1, до 100 (%). Описывает то, какая доля потребляемой мощности является активной и будет переходить в тепло. Остальная мощность будет принята за реактивную;', 
                    'Максимальное тепловыделение от 1 узла. ', 
                    'Количество коммутаторов системы соответствующего типа, которые нужно охлаждать.',
                ],
            outcomes: [
                    'Число кондиционеров в штуках, требуемое для охлаждения системы. '
                ], 
            description: `Модуль расчета системы охлаждения предназначен для вычисления минимального необходимого числа кондиционеров для обеспечения отвода тепла от оборудования..`,
            disclaimers: [
            "Конфигурация кондиционеров следующая - охладительные системы устанавливаются на стенках стоек, чтобы они под давлением вводили холодный воздух, из-за чего горячий выйдет через все доступные отверстия;",
            "Округление происходит в большую сторону;", 
            "Не обозначает резерва, система будет выведена из строя при поломке хотя бы одного кондиционера."           
            ]
        },

    }),
    methods:{
        start(){
            this.started = true
            let result = 0;
            if(this.komutat_opt==false){
                this.komutat_yadr = 0
                this.komutat_dost = 0
                this.count_yadr=0
                this.count_dost=0
                this.komutat_klast=0
            }

            console.log(this.WattOption)
            if(this.WattOption==false){

                result = (
                    (this.Heat*this.K_sred/100*this.Usli_shd)/3.41 
                    // +this.komutat_klast/3.41
                    +(this.count_yadr*this.komutat_yadr+this.count_dost*this.komutat_dost)/3.41
                    )
                // console.log('result', result)
                // console.log('Heat', this.Heat)
                // console.log('K_sred', this.K_sred)
                // console.log('Usli_shd', this.Usli_shd)
                // console.log('komutat_dost', this.komutat_dost)
                // console.log('komutat_opt', this.komutat_opt)
                // console.log('Condition', this.condition)
                this.final_result= Math.ceil(result/this.condition)
            }else{


                
                result = (this.Heat * (this.K_sred/100) * this.Usli_shd)

                // result += this.komutat_klast

                result += this.count_yadr * this.komutat_yadr + this.count_dost * this.komutat_dost


                // console.log('result', result)
                // console.log('Heat', this.Heat)
                // console.log('K_sred', this.K_sred)
                // console.log('Usli_shd', this.Usli_shd)
                // console.log('komutat_dost', this.komutat_dost)
                // console.log('komutat_opt', this.komutat_opt)
                // console.log('Condition', this.condition)
                this.final_result= Math.ceil(result/this.condition)
            }
            // console.log('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA', this.final_result)
            if (!this.final_result || this.final_result== Infinity){
                this.snackbar = true
                this.started = false
                return
            }
        },
        getusli(){
            this.Usli_shd = localStorage.getItem('usli')
            console.log(this.Usli_shd)
            return this.Usli_shd
        },
        komutat_Opt(){
            
            if(this.komutat_opt==false){
                this.komutat_opt = true
            }else{ this.komutat_opt = false}

            this.komutat_opt != this.komutat_opt
            if(this.komutat_opt==false){
                this.komutat_yadr = 0
                this.komutat_dost = 0
                this.count_yadr=0
                this.count_dost=0
            }else{
                this.komutat_yadr = '...'
                this.komutat_dost = '...'
                this.count_yadr='...'
                this.count_dost='...'
            } 
        },
    },
    mounted(){
        this.getusli()
    },
    computed: {
        measureUnit: function () {
            if (this.WattOption) return 'Вт'
            else return 'BTU/hr'
        },

        condition_label: function () {
            return `Мощность кондиционера (${this.measureUnit})       `
        },

        condition_label_description: function () {
            return `Мощность, которую способен ответсти один кондиционер. Измеряется в ${this.measureUnit}       `
        },


        heat_label: function () {
            return `Максимальное тепловыделение (${this.measureUnit})       `
        },
        heat_label_description: function () {
            return this.componentInfo.incomes[2].toString() +` Измеряется в ${this.measureUnit}       `
        },
        

        komutat_yadr_label: function () {
            return `Тепловыделение коммутатора ядра (${this.measureUnit})       `
        },
        komutat_yadr_label_description: function () {
            return `Тепловыделение коммутатора ядра. Измеряется в ${this.measureUnit}       `
        },


        komutat_dost_label: function () {
            return `Тепловыделение коммутатора доступа (${this.measureUnit})       `
        },
        komutat_dost_label_description: function () {
            return `Тепловыделение коммутатора доступа. Измеряется в ${this.measureUnit}       `
        },
    }
}
</script>

<style>

</style>