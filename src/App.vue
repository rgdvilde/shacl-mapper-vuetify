<template>
  <div id="app">
    <v-app>
      <div class="container">
        <h1>Shacl-Mapper</h1>
        <v-form v-model="valid">
          <v-container>
            <v-row>
              <v-text-field
                v-model="shapeFileUri"
                label="Shapes File"
              />
              <v-btn
                depressed
                @click.prevent="loadShapes"
              >
                Load
              </v-btn>
            </v-row>
            <v-row>
              <v-text-field
                v-model="url"
                label="URL"
              />
              <v-btn
                depressed
                @click.prevent="loadEndpoint"
              >
                Load
              </v-btn>
            </v-row>
          </v-container>
        </v-form>
        <shacl-mapper
          :shapes-graph-text="shapesText"
          :options="options"
          :endpoint-data="endpointdata"
        />
      </div>
    </v-app>    
  </div>
</template>

<script>
import axios from 'axios'
import * as $rdf from 'rdflib'
import ShaclMapper from './ShaclMapper'

export default {
  data() {
    return {
      shapeFileUri: document.location.origin + '/ngsi.ttl',
      targetClass: '',
      shapesText: '',
      dataText: '',
      shapesGraph: $rdf.graph(),
      targetShapes: [],
      options: {},
      endpointdata: {},
      url: 'https://data.stad.gent/api/records/1.0/search/?dataset=donkey-republic-deelfietsen-stations-locaties&q=',
    }
  },
  mounted() {
    this.loadShapes()
    this.loadEndpoint()
  },
  methods: {
    onLoad(shapes) {
      this.targetShapes = shapes
    },
    loadShapes() {
      this.shapesGraph = $rdf.graph();
      axios.get(this.shapeFileUri).then(response => {
        this.shapesText = response.data
      }).catch(e => {
        alert('failed to load ' + this.shapeFileUri + '\n' + e)
      })
    },
    loadEndpoint() {
      axios.get(this.url).then(response => {
        this.endpointdata = response.data
      }).catch(e => {
        alert('failed to load ' + this.url + '\n' + e)
      })
    }
  },
  components: {
    ShaclMapper
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
</style>