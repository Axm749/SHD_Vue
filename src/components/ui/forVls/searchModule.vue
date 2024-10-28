
<template>
	
    <v-menu offset-y :close-on-content-click="false">
          <template v-slot:activator="{ on, attrs }">
            <v-btn icon v-bind="attrs" v-on="on">
              <v-icon small :color="term ? 'orange' : ''">
                mdi-magnify
              </v-icon>
            </v-btn>
          </template>
        <v-card>
            <v-text-field
                v-model="term"
                class="pa-4"
                type="text"
                label="Enter the search term"
                :autofocus="true"
                clearable
                outlined
                @input="output"
            ></v-text-field>
        </v-card>
    </v-menu>

</template>

<script>

export default {
name: 'searchForColumn',
props: {
    info: String,
},
data () {
    return {
        term: '',
    }
    
},

methods: {
    output(){
        // console.log('hi from text')
        this.$emit('info', this.term)
    },
},
}
</script>

