<script setup>
  import { ref, onMounted, reactive, computed } from 'vue';
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

  const cotizacion = ref({})


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
      setTimeout(() => {
        error.value = ''
      }, 3000)
      return
    }

    obtenerCotizacion()
  }

  const obtenerCotizacion = async () => {
    const { moneda, criptomoneda } = cotizar
    const url = `https://min-api.cryptocompare.com/data/pricemultifull?fsyms=${criptomoneda}&tsyms=${moneda}`
    
    const respuesta = await fetch(url)
    const data = await respuesta.json()

    // Asignar al State
    cotizacion.value = data.DISPLAY[criptomoneda][moneda]

  }

  const mostrarResultado = computed(() => {
     return Object.values(cotizacion.value).length > 0
  })

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

      <div v-if="mostrarResultado" class="contenedor-resultado">
        <h2>Cotización</h2>
        <div class="resultado">
          <img 
            :src="'https://cryptocompare.com/' + cotizacion.IMAGEURL" 
            alt="imagen cripto"
          />
          <div>
            <p>El precio es de: <span>{{ cotizacion.PRICE }}</span></p>
            <p>El precio mas alto del día: <span>{{ cotizacion.HIGHDAY }}</span></p>
            <p>El precio más bajo del día: <span>{{ cotizacion.LOWDAY }}</span></p>
            <p>Variación ultimas 24 horas: <span>{{ cotizacion. CHANGEPCT24HOUR }}%</span></p>
            <!-- <p>Ultima Actualización: <span>{{ cotizacion. }}</span></p> -->
          </div>
        </div>

      </div>
    </div>
  </div>
</template>
