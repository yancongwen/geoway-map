<template>
  <div class="map-wrap">
    <div class="toolbar">
      <div class="btn" @click="clearDraw()">清除交互</div>
      <div class="btn" @click="clearLayer()">清除图层</div>
      <div class="btn" @click="addInteraction('Polygon')" :class="{active: actived === 'Polygon'}">面标绘</div>
      <div class="btn" @click="addInteraction('LineString')" :class="{active: actived === 'LineString'}">线标绘</div>
      <div class="btn" @click="addInteraction('Point')" :class="{active: actived === 'Point'}">点标绘</div>
    </div>
    <div id="map"></div>
  </div>
</template>

<script>
import { Map, View } from 'ol'
import TileLayer from 'ol/layer/Tile'
import TileImage from 'ol/source/TileImage'
import TileGrid from 'ol/tilegrid/TileGrid'
import {getWidth, getTopLeft} from 'ol/extent.js'
import {get as getProjection} from 'ol/proj.js'
import {Vector as VectorSource} from 'ol/source.js'
import {Vector as VectorLayer} from 'ol/layer.js'
import Draw from 'ol/interaction/Draw.js'

export default {
  data () {
    return {
      actived: ''
    }
  },
  props: ['layerIds'],
  methods: {
    initMap () {
      let layers = this.getLayers()
      this.map = new Map({
        target: 'map',
        layers: layers,
        view: new View({
          projection: 'EPSG:4326',
          center: [98.633, 31.607],
          zoom: 4,
          minZoom: 2,
          maxZoom: 14
        })
      })
      // 添加标绘图层
      this.drawSource = new VectorSource({wrapX: false})
      this.drawLayer = new VectorLayer({
        source: this.drawSource,
        index: 100
      })
      this.map.addLayer(this.drawLayer)
    },
    getLayers () {
      let layers = []
      let ids = ['vec_c', 'eva_c', 'cva_c']
      ids.forEach(item => {
        let layer = this.getTdtlayer(item)
        layers.push(layer)
      })
      return layers
    },
    getTdtlayer (lyr) {
      var url = 'http://t0.tianditu.com/DataServer?T=' + lyr + '&X={x}&Y={y}&L={z}'
      var projection = getProjection('EPSG:4326')
      var projectionExtent = [-180, -90, 180, 90]
      var maxResolution = (getWidth(projectionExtent) / (256 * 2))
      var resolutions = new Array(16)
      for (let z = 0; z < 16; ++z) {
        resolutions[z] = maxResolution / Math.pow(2, z)
      }
      var tileOrigin = getTopLeft(projectionExtent)
      var layer = new TileLayer({
        visible: false,
        extent: [-180, -90, 180, 90],
        source: new TileImage({
          crossOrigin: 'anonymous',
          tileUrlFunction: function (tileCoord) {
            var z = tileCoord[0] + 1
            var x = tileCoord[1]
            var y = -tileCoord[2] - 1
            var n = Math.pow(2, z + 1)
            x = x % n
            if (x * n < 0) {
              x = x + n
            }
            return url.replace('{z}', z.toString())
              .replace('{y}', y.toString())
              .replace('{x}', x.toString())
          },
          projection: projection,
          tileGrid: new TileGrid({
            origin: tileOrigin,
            resolutions: resolutions,
            tileSize: 256
          })
        })
      })
      layer.id = lyr
      return layer
    },
    refreshMap () {
      let layerIds = this.layerIds
      this.map.getLayers().forEach(layer => {
        if (layer.id) {
          if (layerIds.indexOf(layer.id) > -1) {
            layer.setVisible(true)
          } else {
            layer.setVisible(false)
          }
        }
      })
    },
    addInteraction (type) {
      this.actived = type
      this.draw && this.map.removeInteraction(this.draw)
      this.draw = new Draw({
        source: this.drawSource,
        type: type,
        freehand: true
      })
      this.map.addInteraction(this.draw)
    },
    clearLayer () {
      this.actived = ''
      this.drawSource.clear()
    },
    clearDraw () {
      this.actived = ''
      this.map.removeInteraction(this.draw)
    }
  },
  watch: {
    layerIds: function () {
      this.refreshMap()
    }
  },
  mounted () {
    this.initMap()
    this.refreshMap()
  }
}
</script>

<style scoped>
.map-wrap {
  position: relative;
  width: 100%;
  height: 100%;
}
.toolbar {
  height: 28px;
  background-color: #eee;
  border: 1px solid #ccc;
}
#map {
  position: absolute;
  top: 30px;
  bottom: 0;
  width: 100%;
}
.btn {
  height: 26px;
  line-height: 26px;
  text-align: center;
  float: right;
  font-size: 12px;
  margin: 0 5px;
  padding: 0 5px;
  cursor: pointer;
  border: 1px solid #cccccc;
  border-radius: 2px;
  background-color: #eee;
}
.active {
  background-color: #fff;
  color: rgb(40,92,149);
  font-weight: 600; 
}
</style>
