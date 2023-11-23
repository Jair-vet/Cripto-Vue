<script setup>
  import { ref, onMounted, reactive } from 'vue';
  import Alerta from './components/Alerta.vue'
  
  const monedas = ref([
    { codigo: 'USD', texto: 'Dolar de Estados Unidos'},
    { codigo: 'MXN', texto: 'Peso Mexicano'},
    { codigo: 'EUR', texto: 'Euro'},
    { codigo: 'GBP', texto: 'Libra Esterlina'},
  ])
  
  const criptomonedas = ref([]) 
  const error = ref('')

  const cotizar = reactive({
    moneda: '',
    criptomoneda: ''
  })

  onMounted(() => {
    fetch('https://min-api.cryptocompare.com/data/top/mktcapfull?limit=10&tsym=USD')
      .then(respuesta => respuesta.json())
      .then(({Data}) => {
        criptomonedas.value = Data
      })
  })

  const cotizarCripto = () => {
    // Validar que cotizar este lleno
    if(Object.values(cotizar).includes('')){
      error.value = 'Todos los Campos son Obligatorios'
      return
    }
    setTimeout(() => {
      error.value = ''
    }, 3000)

    obtenerCotizacion()
  }

  const obtenerCotizacion = async () => {
    const { moneda, criptomoneda } = cotizar
    const url = `https://min-api.cryptocompare.com/data/pricemultifull?fsyms=${criptomoneda}&tsyms=${moneda}`
    
    
  }

</script>

<template>
  <div class="contenedor">
    <h1 class="titulo">Cotizador de <span>Criptomonedas</span></h1>

    <div class="contenido">
      <form 
        class="formulario"
        @submit.prevent="cotizarCripto"
      >
        <Alerta 
            v-if="error"
        >{{ error }}</Alerta>

        <div class="campo">
          <label for="moneda">Moneda:</label>
          <select 
            id="moneda"
            v-model="cotizar.moneda"
          >
            <option value="">- - Selecciona - -</option>
            <option 
              v-for="moneda in monedas" 
              v-bind:key="moneda.codigo"
              :value="moneda.codigo"
            >{{ moneda.texto }}</option>

          </select>
        </div>

        <div class="campo">
          <label for="cripto">CriptoMoneda:</label>
          <select 
            id="cripto"
            v-model="cotizar.criptomoneda"
          >
            <option value="">- - Selecciona - -</option>
            <option 
              v-for="criptomoneda in criptomonedas" 
              v-bind:key="criptomoneda.codigo"
              :value="criptomoneda.CoinInfo.Name"
            >{{ criptomoneda.CoinInfo.FullName }}</option>

          </select>
        </div>

        <input type="submit" value="Cotizar"/>
      </form>
    </div>
  </div>
</template>
