<template>
  <v-layout :wrap="true">
    <v-flex xs12>
      <v-card>
         <v-date-picker 
            v-model="fecha" 
            full-width 
            locale="es-es" 
            color="info"
            :min="minimo"
            :max="maximo"
            @change="getDolar(fecha)"
          ></v-date-picker>
      </v-card>
      <v-card color="secondary" dark>
        <v-card-text class="display-1 text-xs-center">
          {{dolar}}
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
  import axios from 'axios'
  import {mapMutations} from 'vuex'
  export default {
    data() {
      return {
        fecha: new Date().toISOString().substr(0, 10),
        minimo: '1984-01-01s',
        maximo: new Date().toISOString().substr(0, 10),
        dolar: ''
      }
    },
    methods: {
      ...mapMutations(['mostrarLoading', 'ocultarLoading']),
      async getDolar(dia) {
        let arrayFecha = dia.split(['-'])
        let ddmmyyyy = arrayFecha[2]+"-"+arrayFecha[1]+"-"+arrayFecha[0]
        try {
          this.mostrarLoading({titulo: 'Buscando información', color: 'secondary'})
          const url = `https://mindicador.cl/api/dolar/${ddmmyyyy}`
          let datos = await axios.get(url)
          if(datos.data.serie.length > 0){
            this.dolar = "$" + await datos.data.serie[0].valor
          } else {
            this.dolar = "Sin datos"
          }
        } catch(e) {
          console.error(e)
        } finally {
          this.ocultarLoading()
        }
        
      }
    },
    created() {
      this.getDolar(this.fecha)
    }
  }
</script>
