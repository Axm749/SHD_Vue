<template>
    <div>
      <v-img src="../images/climateWindMap.gif"></v-img>
      <br />
      <v-data-table
        v-bind:headers="headers"
        v-bind:items="item"
        hide-default-footer
        multi-sort
        @click:row="onClickRow"
      >
        <!-- <template v-slot:header.city="{ header }">
          {{ header.text }}
          <searchForColumn @info="writeSearchCity" />
        </template>
        <template v-slot:header.area="{ header }">
          {{ header.text }}
          <searchForColumn @info="writeSearchArea" />
        </template>
        <template v-slot:header.airZone="{ header }">
          {{ header.text }}
          <searchForColumn @info="writeSearchAirZone" />
        </template>
        <template v-slot:header.Norma="{ header }">
          {{ header.text }}
          <searchForColumn @info="writeSearchNorma" />
        </template>
        <template v-slot:header.documentation="{ header }">
          {{ header.text }}
          <searchForColumn @info="writeSearchDocumentation" />
        </template>
        <template v-slot:header.maps="{ header }">
          {{ header.text }}
          <searchForColumn @info="writeSearchMaps" />
        </template> -->
      </v-data-table>
  
      <br />
      <br />
    </div>
  </template>
  
  <script>
  // import searchForColumn from "../SearchModule.vue";
  export default {
    name: "AirZoneTable",
    components: {
      // searchForColumn,
    },
    props: {
      data: Array,
    },
    data: () => ({
      search: "",
      selected: [],
      logs: [],
      City: "",
      Area: "",
      AirZone: "",
      norma: "",
      Documentation: "",
      Maps: "",
      headers: [
        { text: "Город", value: "city", align: "left" },
        { text: "Область, край, республика", value: "area" },
        { text: "Ветровой район", value: "airZone" },
        {
          text: "Нормативное значение ветрового давления, w0, кПа",
          value: "Norma",
        },
        { text: "Нормативный документ", value: "documentation" },
        { text: "№ карты", value: "maps" },
      ],
      item: [
        {
          city: "Абаза",
          area: "Республика Хакасия",
          airZone: "III",
          Norma: 0.38,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Абакан",
          area: "Республика Хакасия",
          airZone: "III",
          Norma: 0.38,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Абдулино",
          area: "Оренбургская область",
          airZone: "III",
          Norma: 0.38,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Агинское",
          area: "Забайкальский край",
          airZone: "III",
          Norma: 0.38,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Ай-Петри",
          area: "Республика Крым",
          airZone: "VII",
          Norma: 0.85,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2e",
        },
        {
          city: "Айхал",
          area: "Республика Саха(Якутия)",
          airZone: "I",
          Norma: 0.23,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Ак-Довурак",
          area: "Республика Тыва",
          airZone: "III",
          Norma: 0.38,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Алакуртти",
          area: "Мурманская область",
          airZone: "I",
          Norma: 0.23,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2в",
        },
        {
          city: "Алапаевск",
          area: "Свердловская область",
          airZone: "I",
          Norma: 0.23,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Алатырь",
          area: "Чувашская Республика",
          airZone: "II",
          Norma: 0.30,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Алдан",
          area: "Республика Саха(Якутия)",
          airZone: "I",
          Norma: 0.23,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Алейск",
          area: "Алтайский край",
          airZone: "III",
          Norma: 0.38,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Александров-Гай",
          area: "Саратовская область",
          airZone: "III",
          Norma: 0.38,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
        {
          city: "Александровск-Сахалинский",
          area: "Сахалинская область",
          airZone: "V",
          Norma: 0.60,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2а",
        },
        {
          city: "Аллах-Юнь",
          area: "Республика Саха(Якутия)",
          airZone: "I",
          Norma: 0.23,
          documentation: "СП 20.13330.2016 Таблица 11.1",
          maps: "Карта 2",
        },
      ],

      table: [
        {
            // 1 район
            windPressure: 400,
            windSpeed: 25
        },
        {
            // 2 район
            windPressure: 500,
            windSpeed: 29
        },
        {
            // 3 район
            windPressure: 650,
            windSpeed: 32
        },
        {
            // 4 район
            windPressure: 800,
            windSpeed: 36
        },
        {
            // 5 район
            windPressure: 1000,
            windSpeed: 40
        },
        {
            // 6 район
            windPressure: 1250,
            windSpeed: 45
        },
        {
            // 7 район
            windPressure: 1500,
            windSpeed: 49
        },
        {
            // Особый район
            // он намеренно невменяемый
            windPressure: 3000,
            windSpeed: 100
        },
        
        
      ],
    }),
  
    computed: {
      filteredAirZones() {
        let conditions = [];
        // console.log(conditions)
        if (this.city) {
          conditions.push(this.filterCity);
        }
        if (this.area) {
          conditions.push(this.filterArea);
        }
        if (this.AirZone) {
          conditions.push(this.filterAirZone);
        }
        if (this.norma) {
          conditions.push(this.filterNorma);
        }
        if (this.Documentation) {
          conditions.push(this.filterDocumentation);
        }
        if (this.Maps) {
          conditions.push(this.filterMaps);
        }
        if (conditions.length > 0) {
          return this.logs.filter((logs) => {
            return conditions.every((condition) => {
              return condition(logs);
            });
          });
        }
        // если ничего не нашли, так уж и быть
        console.log("nothing found");
        return this.logs;
      },
    },
    methods: {
        writeSearchCity(data) {
            this.City = data;
        },
        writeSearchArea(data) {
            this.Area = data;
        },
        writeSearchAirZone(data) {
            this.AirZone = data;
        },
        writeSearchNorma(data) {
            this.norma = data;
        },
        writeSearchDocumentation(data) {
            this.Documentation = data;
        },
        writeSearchMaps(data) {
            this.maps = data;
        },
    
        async onClickRow(item) {
            
            this.selected = [
            {
                city: item.city,
                area: item.area,
                airZone: item.airZone,
                Norma: item.Norma,
                windPressure: this.zoneNumToWindPressure(this.rimeToNumber(item.airZone)),
                windSpeed: this.zoneNumToWindSpeed(this.rimeToNumber(item.airZone)),
                
            },
            ];
            console.log("selected: ", this.selected);
            this.$emit("getAirZoneSelected", this.selected);
        },
        filterCity(item) {
            return item.city.toString().includes(this.city.toLowerCase());
        },
        filterArea(item) {
            return item.area.toLowerCase().includes(this.area.toLowerCase());
        },
        filterAirZone(item) {
            return item.airZone.toString().includes(this.airZone.toLowerCase());
        },
        filterNorma(item) {
            return item.Norma.toLowerCase().includes(this.Norma);
        },
        filterDocumentation(item) {
            return item.documentation
            .toLowerCase()
            .includes(this.documentation.toLowerCase());
        },
        filterMaps(item) {
            return item.maps.toString().includes(this.maps.toLowerCase());
        },



      
        //   римские цифры в обычные
        rimeToNumber(rimeNum){
            if (rimeNum == 'I')         return 1
            if (rimeNum == 'II')        return 2
            if (rimeNum == 'III')       return 3
            if (rimeNum == 'IV')        return 4
            if (rimeNum == 'V')         return 5
            if (rimeNum == 'VI')        return 6
            if (rimeNum == 'VII')       return 7
            if (rimeNum == 'SPECIAL')   return 6
        },

        zoneNumToWindPressure(zoneNum){
            return this.table[zoneNum-1].windPressure
        },
        zoneNumToWindSpeed(zoneNum){
            return this.table[zoneNum-1].windSpeed
        }

    },
  };
  </script>
  
  <style></style>
  