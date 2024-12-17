<template>
    <div>
        <!-- через геометрические параметры -->
        <div class="module_bg">
            
            <!-- заголовок -->
            <div class="header">
                <headerTab 
                    title="Расчёт выходного битрейта видеокамер и PPM"
                    v-bind:info = "componentInfo"
                ></headerTab>
            </div>
            
            <!-- поле ввода -->
            <v-card
                class="pa-5"
                tile
            >
                    
                <!-- <h1>Видео (битрейт и PPM)</h1> -->
                
                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="PPM_zones"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title="Число зон PPM"
                            :desc="componentInfo.incomes[0]"
                            imgRole="cameraTips"
                            tipUrl="Зоны_PPM.png"
                        />
                    </template>
                </v-text-field>

                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="Cam_height"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title="Высота установки камеры (м)"
                            :desc="componentInfo.incomes[1]"
                            imgRole="cameraTips"
                            tipUrl="Высота_установки_камеры.png"
                        />
                    </template>
                </v-text-field>

                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="cam_angle"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title="Угол наклона камеры (градусы)"
                            :desc="componentInfo.incomes[2]"
                            imgRole="cameraTips"
                            tipUrl="Угол_наклона_камеры.png"
                        />
                        <!-- Угол_наклона_камеры.png -->
                    </template>
                </v-text-field>

                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="Vertical_cam_angle"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title="Вертикальный угол обзора объектива (градусы)"
                            :desc="componentInfo.incomes[3]"
                            imgRole="cameraTips"
                            tipUrl="Вертикальный_угол_обзора.png"
                        />
                    </template>
                </v-text-field>
                
                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    label="Горизонтальный угол обзора объектива (градусы)"
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="Horizontal_cam_angle"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title="Горизонтальный угол обзора объектива (градусы)"
                            :desc="componentInfo.incomes[4]"
                            imgRole="cameraTips"
                            tipUrl="Горизонтальный_уол_камеры.png"
                        />
                    </template>
                </v-text-field>
                    
                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="Camera_reach"
                    class="mt-5"
                > 
                    <template v-slot:label>
                        <toolbarInfo
                            title="Дальность обзора (м)"
                            :desc="componentInfo.incomes[5]"
                            imgRole="cameraTips"
                            tipUrl="Максимальная_длина_обзора_камеры.png"
                        />
                    </template>
                </v-text-field>
                
                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="Resolution_X"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title="Разрешение по горизонтали (пикселей)"
                            desc="количество пикселей на матрице по горизонтали"
                        />
                    </template>
                </v-text-field>

                <v-text-field
                    flat
                    type="number"
                    required
                    outlined
                    clearable
                    label="Разрешение по вертикали (пикселей)"
                    :rules="rule"
                    hide-details="auto"
                    v-model.number="Resolution_Y"
                    class="mt-5"
                >
                    <template v-slot:label>
                        <toolbarInfo
                            title="Разрешение по вертикали (пикселей)"
                            desc="количество пикселей на матрице по вертикали"
                        />
                    </template>                
                </v-text-field>

                <v-row>
                    <v-col cols="100%">
                        <v-text-field
                            type="number"
                            :disabled="self === false "
                            outlined
                            clearable
                            label="Средняя доля сжатия с кодаком (0,0 - 1,0)"
                            :rules="rule"
                            hide-details="auto"
                            v-model="kodak"
                            class="mt-5"
                        />
                    </v-col>
                    <v-col cols="4">
                        <v-checkbox 

                        info
                        hide-details
                        label="Ввести вручную?"
                        v-model="self"
                        class="mt-8"
                        />
                    </v-col>
                </v-row>
                

                
                
                <v-btn 
                    @click="start" 
                    color="primary"
                    class="mt-5"
                    width="100%"
                >Старт</v-btn>



                <br>
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
                            {{ final_mBR_visualised }}
                        </v-chip> 
                    </p>
                    </v-col>
                    <v-col cols="5">
                        <v-btn
                        class="mt-3"
                        @click="started=false"
                        v-if="started"
                        >
                            скрыть
                        </v-btn>
                    </v-col>
                </v-row>
                

                <v-divider/>

                <v-simple-table
                v-if="started" 
                fixed-header
                height="200px">
                    <thead>
                        <tr>
                        <th class="text-left">
                            Номер зоны
                        </th>
                        <th class="text-left">
                            PPM (пикселей/м^2)
                        </th>
                        <th class="text-left">
                            Покрываемая площадь (м^2)
                        </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="i in PPM_zones" :key="i">
                            <td>{{i}} </td>
                            <td> 
                                {{PPM[i]}}
                            </td>
                            <td> {{ (+S[i]).toFixed(1) }} </td>
                        </tr>
                    </tbody>
                </v-simple-table>
            </v-card>
        </div>
 
    </div>
</template>

<script>
import headerTab from "./ui/commonUi/header.vue";
import toolbarInfo from './ui/commonUi/tooltip.vue';

export default {
    components: {
        headerTab,
        toolbarInfo,
    },
    name: 'video_vue',
    data:() =>({
        // для пункта с информацией о модуле
        componentInfo: {
            name: 'Расчёт выходного битрейта видеокамер и PPM',
            incomes: [
            'Число зон PPM', 
            'Высота установки камер',
            "Угол наклона камеры",
            "Вертикальный угол обзора объектива",
            "Горизонтальный угол обзора объектива",
            "Дальность обзора",
            "Разрешение по горизонтали",
            "Разрешение по вертикали",
            "Средняя доля сжатия кодаком",
            "(для обратного) Плотность изображения",
            "(для обратного) Покрываемая площадь",
            
            ],
            outcomes: [
            'Плотность изображения на каждой зоне',
            'Площадь, покрываемая этой зоной'
            ], 
            description: `Программа на основании только входных данных пользователя о видеокамере рассчитывает следующие параметры: PPM(пикселей/м2), Битрейт (мб/с) и количество пикселей на каждой зоне обзора. 
                Для расчета битрейта на выходе видеокамеры необходимо знать ряд различных параметров. От характеристик камер, например числа мегапикселей матрицы или частоты съёмки, до геометрических параметров расположения камеры и характеристик объектива.
                В программе для расчета битрейта устройства учитываются данные, вводимые пользователем и, по необходимости, изменяемые, для удовлетворения реалистичности вычислений (невозможные в реальности параметры не допускаются).`,
            disclaimers: [
                'За основу взяты условия, что камера статична, не перемещается, не поворачивается и не ведет дополнительных замеров кроме съемки изображения с кадровой частотой в формате H2643. Участок обзора камеры имеет ограниченную дистанцию и всё, что находится дальше, в расчете не учитывается. На участке нет преграждающих обзор стен, и камера не направлена выше линии горизонта.',
                'округления идут в большую сторону, предполагается наихудший сценарий'
            ]
        
        },
        
        PPM_zones: 2, //Зоны PPM
        Cam_height:3, //Высота установки камеры
        cam_angle:60, //Угол наклона камеры
        Vertical_cam_angle:50,  //Вертикальный угол обзора камеры
        Horizontal_cam_angle:80,//Горизонтальный угол обзора камеры
        Camera_reach:25,        //Дальность обзора камеры
        Resolution_X:1920,      //Разрешение по горизонтали
        Resolution_Y:1080,      //Разрешение по вертикали
        plot:'...',             //Плотность изображения
        square:'...',           //Покрываемая площадь
        kodak:0.15,             //Параметр кодака
        self:false,             //Параметр, отвечающий за ручной ввод для кодака
        started:false,          //Параметр, отвечающий за вывод результатов работы программы при нажатии "Старт"
        analog:false,           //Параметр, отвечающий за вывод результатов работы программы при аналоговом расчёте
        rule: [
            value => !!value || 'Необходимо заполнить это поле.',
        ],                              //Правила для текстовых полей
        Angle_Height_LowFieldOfView:0,  //Угол, необходимый для рассчёта длины слепой зоны камеры
        L_blind:0,                      //Длина слепой зоны камеры
        dx:0,                           //
        d:0,
        S:[1],
        f:0,
        Total:0,
        Total_Resolution:0,
        i:1,
        bottom:0,
        top:0,
        PPM:[1],
        Outer_angle:0,
        Real_L_max:0,
        accept:false,
        mBr:0,
        final_mBR:0,
        L_big:0,
        Temp_f_big:0,
        L_small:0,
        Temp_f_small:0,
        HIPOTENUSE:0,
        HIPOTENUSE1:0,
        result:[1],
        pixelscount:0,
    }),

    methods:{
        start(){
            if (this.PPM_zones>=10) this.PPM_zones = 10
            if (this.PPM_zones.toFixed(0) != this.PPM_zones) this.PPM_zones = this.PPM_zones.toFixed(0)
            
            this.Total = 0
            this.result[this.i]=0
            this.mBr = 0
            this.final_mBR = 0
            this.d=0
            
            this.started = true

            let FPS = 30;
            this.check_len()
            this.Angle_Height_LowFieldOfView = +this.cam_angle-(+this.Vertical_cam_angle/2)
            // console.log(this.Angle_Height_LowFieldOfView)

            this.L_blind = (+this.Cam_height*Math.tan(this.Angle_Height_LowFieldOfView*Math.PI/180)).toFixed(5)
            // console.log(this.L_blind)
            if((+this.Camera_reach - this.L_blind) <=0){
                alert ('Ошибка!')
            }if (this.L_blind <0){
                alert('Ошибка!')
            }
            this.get_dx()
            this.Total_Resolution = +this.Resolution_X*+this.Resolution_Y
            for (this.i = 1; this.i < +this.PPM_zones+1; this.i++){
                // console.log(`____________________current cycle №${this.i}____________________`);
                // console.log(`____________________f_param____________________`);
                this.get_f();
                // console.log(`____________________d_param____________________`);
                this.get_d();
                // console.log(`____________________Bottom____________________`);
                this.get_Wigth1();
                //bottom
                // console.log(`____________________Top____________________`);
                this.get_Wigth2();
                //top
                // console.log(`____________________S_param____________________`);
                this.trap();
                // console.log(`____________________Total_PPM____________________`);
                this.PPM_from_S()

                
                
                this.Total += (+this.Total_Resolution * +this.d)
                // console.log('total',this.Total)
                this.result[this.i]= +this.Total_Resolution * +this.d
                this.mBr = (+this.Total * FPS * +this.kodak).toFixed(5)
                // console.log(`Our bit rate: ${(this.mBr/(1024*1024)).toFixed(5)}`);
                this.final_mBR = (+this.mBr/(1024*1024)).toFixed(2)

                // здесь будет обработчик ошибки, пока не успели ничего вставить

                
                localStorage.setItem('Bitrate', this.final_mBR)
                this.$emit('cam_bitrate', this.final_mBR)
            }
        },

        degtoRad(degrees) {
                // console.log(`We got: ${degrees} deegrees.`)
                return degrees * (Math.PI/180)
        },
        radtoDeg(radians) {
            // console.log(`We got: ${radians} radians.`)
            return radians * (180/Math.PI);
        },

        get_dx(){
            this.dx = (+this.Camera_reach - this.L_blind) / +this.PPM_zones
            // console.log(`dx we calculated: ${this.dx}`)
        },
        get_f(){
            // console.log('asdad',this.L_blind)
            // console.log(this.dx)
            this.L_big = +this.L_blind + (+this.dx * +this.i)
            // console.log(`L_big calculated: ${this.L_big}`)
            this.Temp_f_big = Math.atan(+this.L_big / +this.Cam_height)*180/Math.PI
            // console.log(`Temp_f_big calculated: ${this.Temp_f_big}`)
            this.L_small = (+this.L_blind + (+this.dx * (+this.i - 1)))
            // console.log(`L_small calculated: ${this.L_small}`)
            this.Temp_f_small = Math.atan(+this.L_small / +this.Cam_height)*180/Math.PI
            // console.log(`Temp_f_small calculated: ${this.Temp_f_small}`)

            if (+this.Camera_reach < ((+this.L_blind + (+this.dx * +this.i))/+this.Cam_height)){
                console.log(` Something is not right. ${((+this.L_blind + (+this.dx * +this.i))/+this.Cam_height)} is bigger than ${+this.Camera_reach}`)
            }

            this.f = +this.Temp_f_big -+ this.Temp_f_small
            // console.log(`f we got from calculations: ${this.f}`)
        },
        
        get_Wigth1(){
            this.HIPOTENUSE = Math.sqrt(((+this.L_blind+(+this.i) * +this.dx)*(+this.L_blind+(+this.i) * +this.dx))+(+this.Cam_height*+this.Cam_height));
            // console.log(`HIPOTENUSE: ${this.HIPOTENUSE} !!!`);
            this.bottom = (2 * +this.HIPOTENUSE * Math.tan(+this.Horizontal_cam_angle/2*Math.PI/180)).toFixed(5)
            // console.log(`width we got: ${this.bottom}`)
        },
        get_Wigth2(){
            this.HIPOTENUSE1 = Math.sqrt(((+this.L_blind+(+this.i-1) * +this.dx)*(+this.L_blind+(+this.i-1) * +this.dx))+(+this.Cam_height*+this.Cam_height));
            // console.log(`HIPOTENUSE: ${this.HIPOTENUSE1} !!!`);
            // console.log('tan',Math.tan(+this.Horizontal_cam_angle/2*Math.PI/180))
            // console.log('hor', this.Horizontal_cam_angle)
            this.top = (2 * +this.HIPOTENUSE1 * Math.tan(+this.Horizontal_cam_angle/2*Math.PI/180)).toFixed(5)
            // console.log(`width we got: ${this.top}`)
        },
        get_d(){
            this.d = (this.f/this.Vertical_cam_angle).toFixed(5)
            // console.log(`the calculation of d param: ${this.d}`);
        },
        trap(){
            // console.log(this.top)
            this.S[this.i] = (+this.top * +this.dx + ((+this.bottom - +this.top) / 2) * +this.dx).toFixed(5)
            // console.log(`S we got: ${this.S[this.i]}`)
            
        },
        PPM_from_S(){
            this.PPM[this.i] = ((+this.Total_Resolution * +this.d) / +this.S[this.i]).toFixed(5);
            // console.log(`PPM of zone ${+this.i} is equal to: ${+this.PPM[this.i]}`);
            // console.log(`Pixels in that zone: ${+this.Total_Resolution * +this.d}`)
        },
        check_len(){
            this.Outer_angle = ( this.cam_angle + (this.Vertical_cam_angle/2))
            this.Real_L_max = (this.Cam_height * Math.tan(this.Outer_angle*Math.PI/180)).toFixed(5)
            // console.log(`Результаты функции CHECK_LEN Outer: ${this.Outer_angle}, Real L_max: ${this.Real_L_max}`);
            if (this.Outer_angle >= 90){
                // console.log(`All good.`)
                return 0
            } if ( this.Camera_reach >= this.Real_L_max){
                this.accept = confirm(`при введенных параметрах, реальная максимальная дальность будет ${this.Real_L_max}, значит какие-то параметры введены неверно. Продолжить с реальной максимальной дальностью?`)
                if ( this.accept === true) {
                    console.log(`Продолжаем с введёнными данными.`);
                    this.Camera_reach = this.Real_L_max;
                    // console.log(this.Camera_reach);
                } else {
                    alert(`Ошибка, введённые данные могут быть неверны`)
                }
            }
        },

    },
    computed: {
        final_mBR_visualised(){
            if((this.final_mBR/1024) <= 1){
                return `${this.final_mBR } Мбит/с`
            }
            return `${(Math.round(this.final_mBR/1024 * 100) / 100 )} Гбит/с`
        }
    },

    }
</script>

<style>

::-webkit-scrollbar {
    width: 0px;
  }

  /* Track */
  /* ::-webkit-scrollbar-track {
    background: #d2c8c8;
    grid-template-columns: repeat(3, 1fr);
  }
  .mycols {
  display: grid;
} */
  /* Handle */
  /* ::-webkit-scrollbar-thumb {
    background: rgb(148, 148, 156);
  } */

  /* Handle on hover */
  ::-webkit-scrollbar-thumb:hover {
    background: #555;
  }
  
.v-dialog{
    box-shadow: none;
}
</style>