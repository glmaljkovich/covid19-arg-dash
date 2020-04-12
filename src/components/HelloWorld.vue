<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      Dashboard de ejemplo que consume los datos de
      <a href="https://apibueno.herokuapp.com" target="_blank" rel="noopener">Argentina Covid-19 API</a>.
    </p>
    <div class="container">
      <div class="row">
        <div class="col-sm">
          <h3>{{totales.confirmados}}</h3>
          <p>CONFIRMADOS</p>
        </div>
        <div class="col-sm">
          <h3>{{totales.muertes}}</h3>
          <p>MUERTES</p>
        </div>
        <div class="col-sm">
          <h3>{{totales.recuperados}}</h3>
          <p>RECUPERADOS</p>
        </div>
      </div>
      <div class="row">
        <div class="col-sm">
          <h3>Casos nuevos por dia</h3>
          <Line1 :v-if="fetched" :chart-data="coso"></Line1>
        </div>
      </div>
      <div class="row">
        <div class="col-sm">
          <h3>Muertes por dia</h3>
          <Line1 :v-if="fetched" :chart-data="coso2"></Line1>
        </div>
      </div>
      <div class="row">
        <div class="col-sm">
          <h3>Recuperados por dia</h3>
          <Line1 :v-if="fetched" :chart-data="coso3"></Line1>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Line1 from "./Line1.vue"
import {API} from "../client"

export default {
  name: 'HelloWorld',
  components: {
    Line1
  },
  data() {
    return {
      colors: [
        "#003f5c",
        "#2f4b7c",
        "#665191",
        "#a05195",
        "#d45087",
        "#f95d6a",
        "#ff7c43",
        "#ffa600",
        "#aeefec",
        "#f78259",
        "#eb4559",
        "#b9ebcc",
        "#ea6227",
        "#342ead",
        "#ff5733",
        "#c70039",
        "#900c3f",
        "#511845",
        "#f4e04d",
        "#f2ed6f",
        "#cee397",
        "#8db1ab",
        "#00bcd4",
        "#f95d6a",
      ],
      fetched: false,
      coso: null,
      coso2: null,
      coso3: null,
      totales: {
        confirmados: 0,
        muertes: 0,
        recuperados: 0
      }
    }
  },
  props: {
    msg: String
  },
  created() {
    API.get("confirmados")
    .then(({data}) => {
      const datasets = data.lugares.map((lugar, i) => this.createDataset(lugar.provincia, lugar.historico, i))
      const coso = {}
      coso.labels = this.getLabels(data.lugares[0])
      coso.datasets = datasets
      this.coso = coso
      this.fetched = true
      this.totales.confirmados = datasets.reduce((acc, current) => acc + current.data.reduce((acc2, red2) => acc2 + red2, 0), 0)
    })
    API.get("muertes")
    .then(({data}) => {
      const datasets = data.lugares.map((lugar, i) => this.createDataset(lugar.provincia, lugar.historico, i))
      const coso = {}
      coso.labels = this.getLabels(data.lugares[0])
      coso.datasets = datasets
      this.coso2 = coso
      this.fetched = true
      this.totales.muertes = datasets.reduce((acc, current) => acc + current.data.reduce((acc2, red2) => acc2 + red2, 0), 0)
    })
    API.get("recuperados")
    .then(({data}) => {
      const datasets = data.lugares.map((lugar, i) => this.createDataset(lugar.provincia, lugar.historico, i))
      const coso = {}
      coso.labels = this.getLabels(data.lugares[0])
      coso.datasets = datasets
      this.coso3 = coso
      this.fetched = true
      this.totales.recuperados = datasets.reduce((acc, current) => acc + current.data.reduce((acc2, red2) => acc2 + red2, 0), 0)
    })
  },
  methods: {
    getLabels(lugar){
      return Object.keys(lugar.historico)
    },
    createDataset(nombre, historico, index){
      const dataset = {
        label: nombre,
        data: [],
        borderColor: this.colors[index],
        pointBackgroundColor:"#ddd",
        backgroundColor: "transparent"
      }
      for (const dia in historico) {
        dataset.data.push(historico[dia])
      }
      return dataset
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
