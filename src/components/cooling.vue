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
                :rules="rule"
                hide-details="auto"
                v-model.number="SHD_nodes"
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
                v-model.number="single_node_heat"
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

            










            <template>
                
                <v-dialog
                  v-model="dialog1"
                  transition="dialog-bottom-transition"
                  width="80%"
                  aria-hidden="true"
                >
                  <template v-slot:activator="{ props1 }">
                    <v-btn
                      class="mt-2 mt-5"
                      width="100%"
                      color="primary"
                      v-bind="props1"
                      @click="dialog1=!dialog1"
                      ><v-icon>mdi-cog</v-icon>
                      Прочие устройства
                    </v-btn>
                  </template>
        
                  
                  <!-- сами параметры через v-for -->
                  <div class="module_bg">
                    <v-card
                      outlined
                      class="pa-5"
                    >        
        
                      <h1 class="mx-5">Tепловыделение устройств</h1>
                      
        
                      <div 
                        v-for="(commutator, index) in all_commutators_param"
                        :key="index"
                        class="mt-5 inCardTab"
                      >
                        
                        
                        <v-text-field
                          v-model="commutator.title"
                          required
                          outlined
                          clearable
                          label="название"
                          prepend-icon="mdi-table-column"
                          placeholder="hello"
                          :rules="rule"
                          hide-details="auto"
                          class="mt-2"
                        >
                          <template v-slot:label>
                            <toolbarInfo
                              title="название"
                              desc = "__--__"
                            />
                          </template>
                        </v-text-field>
                        
                        <v-text-field
                          v-model="commutator.count"
                          type="number"
                          required
                          outlined
                          clearable
                          prepend-icon="mdi-counter"
                          label="количество (шт)"
                          :rules="rule"
                          hide-details="auto"
                          class="mt-5"
                        >
                          <template v-slot:label>
                            <toolbarInfo
                              title="количество (шт)"
                              desc = "__--__"
                            />
                          </template>
                        </v-text-field>

                        
                        <v-text-field
                          v-model="commutator.heat"
                          type="number"
                          required
                          outlined
                          clearable
                          prepend-icon="mdi-disc-player"
                          :label="`тепловыделение (${ measureUnit })`"
                          :rules="rule"
                          hide-details="auto"
                          class="mt-5"
                        >
                          <template v-slot:label>
                            <toolbarInfo
                              :title="`тепловыделение (${ measureUnit })`"
                              desc = "__--__"
                            />
                          </template> 
                        </v-text-field>
                        
                        <v-btn
                          color="error"
                          @click="deleteItem(commutator, index)"
                          class="mt-5 mx-2 mb-2"
                        >  
                          <v-icon>mdi-trash-can</v-icon>  удалить 
                        </v-btn>
        
                      </div>
        
                      <v-btn
                        color="primary"
                        @click="extend_all_commutators_param_List"
                        class="ma-5"
                      >добавить</v-btn>
        
                      <v-btn
                        color="success"
                        @click="sumItUp"
                        class="mt-5"
                      >суммарный объём</v-btn>
                  
                    </v-card>
                  </div>
                </v-dialog>
              </template>


            

            <v-btn 
                @click="start" 
                color="primary"
                class="mt-2"
                width="100%"
            >Старт</v-btn>



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
        <div 
        class="pa-5 mt-5"
        v-show="started"
        >
        <v-divider/>
        <v-simple-table>
        <template v-slot:default>
            <thead>
                <tr>
                <th class="text-left">
                    Параметр
                </th>
                <th class="text-left">
                    Значение
                </th>
                </tr>
            </thead>

            <tbody>
                
                <tr>
                    <td>Число кондиционеров </td>
                    <td>
                        <v-chip
                        color="primary"
                        dark
                        >
                        {{ answer.final_result }} шт.
                        </v-chip>
                    </td>
                </tr>
                <tr>
                    <td>Суммарное тепловыделение системы </td>
                    <td>
                        <v-chip
                        color="primary"
                        dark
                        >
                        {{ answer.full_heat }} {{ measureUnit }}.
                        </v-chip>
                    </td>
                </tr>
                <tr>
                    <td>Тепловыделение узлов СХД </td>
                    <td>
                        <v-chip
                        dark
                        >
                        {{ answer.shd_heat }} {{ measureUnit }}.
                        </v-chip>
                    </td>
                </tr>
                <tr>
                    <td>Тепловыделение дополнительных устройств системы </td>
                    <td>
                        <v-chip
                        dark
                        >
                        {{ answer.additional_heat }} {{ measureUnit }}.
                        </v-chip>
                    </td>
                </tr>

                <!-- answer.additional_heat -->
                

            </tbody>
        </template>
        </v-simple-table>
        </div>

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
        dialog1: false,
        answer: {},

        komutat_opt: false,     // опция учета дополнительных коммутаторов
        Usli_self: false,       // дать ли пользователю вводить число узлов СХД
        SHD_nodes: '...',        // число узлов СХД


        snackbar: false,      // окошко об ошибке
        timeout: 2500,
        errorText: 'Неверно введены данные или они отсутствуют',


        K_sred:  70,          // Усреднённое тепловыделение в процентах (0-100)
        WattOption: true,           // использование ваттов как единиц измерения
        single_node_heat: 800,    // тепловыделение одного узла
        condition:6000,            // Мощность кондиционера

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

        all_commutators_param: [
            {id: 0, title: `коммутатор кластера`, count: 2, heat: 500},
            {id: 1, title: `видеостена`, count: 2, heat: 1300},
            {id: 2, title: `другое оборудование 1`, count: 3, heat: 1000},
            {id: 3, title: `другое оборудование 2`, count: 1, heat: 1000},
            {id: 3, title: `другое оборудование 3`, count: 1, heat: 1000},
            
        ]

    }),
    methods:{
        
        extend_all_commutators_param_List(){
            this.all_commutators_param.push({
                id: this.all_commutators_param.length, 
                title: `new_name`, 
                count: 1, 
                heat: 100,
            }) 
            
            // console.log(this.convServParam)
        },
    
        deleteItem: function (item, index) {
            if(this.all_commutators_param[index] === item) { 
            // The template passes index as the second parameter to avoid indexOf, 
            // it will be better for the performance especially for one large array
            // (because indexOf actually loop the array to do the match)
                this.all_commutators_param.splice(index, 1)
                // console.log(this.convServParam)
            } else {
                var found = this.all_commutators_param.indexOf(item)
                this.all_commutators_param.splice(found, 1)
            }
        },

        sumItUp(){
            var sumItUpAnswer = 0
            this.all_commutators_param.forEach( commutator_param => {
                sumItUpAnswer += (commutator_param.count*commutator_param.heat)
                // console.log(`display ${commutator_param.title}, ${commutator_param.heat}*${commutator_param.count}`)
                // console.log(`display ${sumItUpAnswer}, ${sumItUpAnswer* (this.K_sred/100)}`)
            })
            
            // console.log( `${sumItUpAnswer} Ватт`)
            // console.log( `${(this.K_sred/100)} K_sred`)
            return sumItUpAnswer
        }, // суммарный объём всех серверов в гиперконвергентной вкладке
                
        
        
        
        
        
        start(){
            this.started = true
            this.answer = { }

            this.answer.shd_heat = Math.ceil(
                this.SHD_nodes*this.single_node_heat * (this.K_sred/100) * (this.K_sred/100) 
            )
            // здесь вопросы где умножить на 0.7 и сколько раз
            this.answer.full_heat = Math.ceil(
                (this.answer.shd_heat+(
                        this.sumItUp()  
                        * (this.K_sred/100) 
                        * (this.K_sred/100)
                    )
                ) 
                
            )
            console.log(this.sumItUp() * (this.K_sred/100)* (this.K_sred/100), "///////////")

            this.answer.final_result = Math.ceil(
                this.answer.full_heat / this.condition
            )

            this.answer.additional_heat = this.sumItUp() * (this.K_sred/100)

            // что я хочу выводить...
            // ну точно общее тепловыделение
            // также число узлов
            // больше ничего и не надо?

            // console.log('AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA', this.answer.final_result)
            if (this.answer.final_result <=0 || this.answer.final_result == Infinity || !this.answer.final_result){
                this.snackbar = true
                this.started = false
                return
            }
        },
        getusli(){
            this.SHD_nodes = localStorage.getItem('usli')
            console.log(this.SHD_nodes)
            return this.SHD_nodes
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
            return this.componentInfo.incomes[2].toString() +` (${this.measureUnit})       `
        },
        heat_label_description: function () {
            return this.componentInfo.incomes[2].toString() +` Измеряется в ${this.measureUnit}       `
        },
    }
}
</script>

<style>

</style>