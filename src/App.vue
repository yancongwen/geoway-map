<template>
  <div id="app">
    <v-header></v-header>
    <div class="main">
      <div class="left">
        <v-tree :nodeList="treeList" :checkable=true @on-select-change="nodeSelectChange"></v-tree>
      </div>
      <div class="right">
        <v-map :layerIds="getLayerIds()"></v-map>
      </div>
    </div>
  </div>
</template>

<script>
import Header from '@/components/Header.vue'
import Tree from '@/components/Tree.vue'
import Map from '@/components/Map.vue'

export default {
  name: 'App',
  data () {
    return {
      treeList: [
        {
          'type': 'folder',
          'collapsed': true,
          'name': '天地图WMTS服务',
          'checked': false,
          'items': [
            {
              'id': 'cva_c',
              'type': 'file',
              'name': '中文地名注记',
              'checked': true
            },
            {
              'id': 'eva_c',
              'type': 'file',
              'name': '英文地名注记',
              'checked': false
            },
            {
              'id': 'vec_c',
              'type': 'file',
              'name': '底图',
              'checked': true
            }
          ]
        }
      ]
    }
  },
  methods: {
    getLayerIds () {
      let ids = []
      this.treeList.forEach(list => {
        if (list.items && list.items.length > 0) {
          list.items.forEach(item => {
            if (item.checked) {
              ids.push(item.id)
            }
          })
        }
      })
      return ids
    },
    nodeSelectChange: function (nodeList) {
      // this.getLayerIds()
    }
  },
  components: {
    'v-header': Header,
    'v-tree': Tree,
    'v-map': Map
  }
}
</script>

<style>
#app {
  height: 100%;
}
.main {
  display: flex;
  position: absolute;
  top: 78px;
  bottom: 0;
  width: 100%;
}
.main > .left {
  width: 300px;
  border-right: 1px solid #ccc;
}
.main > .right {
  flex: 1;
}
</style>
