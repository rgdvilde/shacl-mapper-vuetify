<template>
  <div>
    <form-input
      v-for="(cons, idx) in propertyConstraints"
      :key="idx"
      :subject="subject"
      :property-shape-node="cons.paramValue"
      :shapes-store="shapesStore"
      v-model="quads[idx]"
      @input="onInput"
    />
  </div>
</template>

<script>
  import * as $rdf from 'rdflib'
  import FormInput from './FormInput.vue'

  const SHACL = new $rdf.Namespace('http://www.w3.org/ns/shacl#')
  const RDF = new $rdf.Namespace('http://www.w3.org/1999/02/22-rdf-syntax-ns#')

  export default {
    name: 'FromGroup',
    props: {
      shape: {
      type: Object,
      default() {
          return {}
      }
    },
      subject:{
      type: Object,
      default() {
          return {}
      }
    },
      shapesStore:{
      type: Object,
      default() {
          return {}
      }
    },
    },
    data() {
      return {
        quads: [],
      }
    },
    beforeCreate: function () {
      this.$options.components.FormInput = require('./FormInput.vue').default
    },
    computed: {
      propertyConstraints() {
        return this.shape.constraints.filter(cons => {
          return cons.component.node.equals(SHACL('PropertyConstraintComponent'))
        })
      },
      targetClass() {
        return $rdf.namedNode('http://www.w3.org/ns/r2rml#TriplesMap')
        // implicit class target
        // if (this.shape.context.$shapes.query().match(this.shape.shapeNode, 'rdf:type', 'rdfs:Class').hasSolution())
        //   return this.shape.shapeNode
        // // class-based target
        // return this.shape.context.$shapes.query().getObject(this.shape.shapeNode, 'sh:targetClass')
      }
    },
    methods: {
      onInput() {
        const quads = this.quads.flat().filter(elm => elm)
        if (this.targetClass) {
          const st = $rdf.quad(this.subject, RDF('type'), this.targetClass)
          quads.push(st)
        }
        this.$emit('input', quads)
      }
    },
    components: {
      FormInput
    }
  }
</script>

<style scoped>

</style>
