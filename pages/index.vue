<template>
  <v-layout @mousemove="moveMouseTag" @drag="moveMouseTag">
    <div
      :style="{ left: mouseTag.x + 'px', top: mouseTag.y + 'px' }"
      class="mouse-tag"
    >
      <v-icon color="black">mdi-domain</v-icon>
    </div>
    <draggable
      v-model="myArray"
      group="people"
      class="items-selection"
      :move="checkMove"
      @start="startDrag"
      @end="endDrag"
      @change="dragMouseDown"
    >
      <v-card v-for="element in myArray" :key="element.id" class="mb-3">
        <v-card-text>
          {{ element.name }}
        </v-card-text>
      </v-card>
    </draggable>
    <div ref="network" class="vis-container"></div>
  </v-layout>
</template>

<script>
// import { Network } from 'vis-network/peer/esm/vis-network'
// import { DataSet } from 'vis-data/peer/esm/vis-data'
// import vis from 'vis-network'
// const vis = require('vis-network')
import { Network } from 'vis-network'
import { DataSet } from 'vis-data'

export default {
  data() {
    return {
      network: null,
      nodes: null,
      edges: null,
      myArray: [
        {
          id: 1,
          name: 'nathan',
        },
        {
          id: 2,
          name: 'sabrina',
        },
      ],
      mouseTag: { x: null, y: null },
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    init() {
      // create an array with nodes
      this.nodes = new DataSet([
        { id: 1, label: 'Node 1' },
        { id: 2, label: 'Node 2' },
        { id: 3, label: 'Node 3' },
        { id: 4, label: 'Node 4' },
        { id: 5, label: 'Node 5' },
      ])

      // create an array with edges
      this.edges = new DataSet([
        { from: 1, to: 3 },
        { from: 1, to: 2 },
        { from: 2, to: 4 },
        { from: 2, to: 5 },
        { from: 3, to: 3 },
      ])

      // create a network
      const container = this.$refs.network
      const data = {
        nodes: this.nodes,
        edges: this.edges,
      }
      const options = {
        clickToUse: true,
        physics: {
          maxVelocity: 0.1,
          minVelocity: 0.1,
        },
        manipulation: {
          addNode(nodeData, callback) {
            nodeData.label = 'hello world'
            callback(nodeData)
          },
        },
      }
      this.network = new Network(container, data, options)
      this.network.on('click', this.onClick)
      this.network.on('release', this.onRelease)
      this.network.on('hold', this.onHold)
      this.network.on('select', this.onSelect)
    },
    onClick(value) {
      console.log('onClick -> value', value)
      this.network.addNodeMode()
    },
    onRelease(value) {
      console.log('onRelease -> value', value)
    },
    onHold(value) {
      console.log('onHold -> value', value)
    },
    onSelect(value) {
      console.log('onSelect -> value', value)
    },
    checkMove(evt) {
      console.log('checkMove -> evt', evt)
    },
    startDrag() {
      console.log('startDrag -> startDrag')
      console.log('endDrag -> this.mouseTag', this.mouseTag.x)
      console.log('endDrag -> this.mouseTag', this.mouseTag.y)
      // this.network.addNodeMode()
      return true
    },
    endDrag(e) {
      console.log('endDrag -> e', e)
      console.log('endDrag -> e', e.originalEvent.clientX)
      console.log('endDrag -> e', e.originalEvent.clientY)
      this.network.addNodeMode()
      const id = this.nodes.length + 1
      console.log('endDrag -> id', id)
      this.nodes.add({
        id,
        label: `Node ${id}`,
        x: e.originalEvent.clientX,
        y: e.originalEvent.clientY,
      })
      return true
    },
    moveMouseTag({ clientX: x, clientY: y }) {
      this.mouseTag.x = x
      this.mouseTag.y = y
    },
    dragMouseDown(e) {
      console.log('dragMouseDown -> e', e)
    },
  },
}
</script>
<style>
.vis-container {
  position: relative;
  width: 100%;
  height: 100vh;
}

.mouse-tag {
  position: fixed;
  margin: 1em;
}

.items-selection {
  position: absolute;
  left: 0;
  top: 0;
  width: 100px;
  height: 100%;
  z-index: 2;
}
</style>
