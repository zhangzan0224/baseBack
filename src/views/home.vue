<template>
  <div class="dashboard-container">
    <ul>
      <li :class="isDx ? 'active' : ''" @click="dingxing">地形</li>
      <li :class="isWx ? 'active' : ''" @click="weixin">卫星</li>
      <li :class="isCj ? 'active' : ''" @click="ceiju">测距</li>
      <li :class="isLk ? 'active' : ''" @click="lukuang">路况</li>
    </ul>
    <div id="container" />
  </div>
</template>

<script>
import AMap from 'AMap'
export default {
  name: 'Dashboard',
  components: {},
  data() {
    return {
      map: '', // 地图实例
      xmap: '', // 卫星地图
      rulerTool: '', // 测距
      trafficLayer: '', // 路况
      googleLayer: '', // 地形
      isWx: false,
      isCj: false,
      isLk: false,
      isDx: false
    }
  },
  mounted() {
    this.initMap()
  },
  methods: {
    dingxing() {
      if (this.isDx) {
        this.map.remove(this.googleLayer)
        this.isDx = false
      } else {
        this.map.add(this.googleLayer)
        this.isDx = true
      }
    },
    weixin() {
      if (this.isWx) {
        this.xmap.hide()
        this.isWx = false
      } else {
        this.xmap.show()
        this.isWx = true
      }
    },
    ceiju() {
      // 自定义样式
      const startMarkerOptions = {
        icon: new AMap.Icon({
          size: new AMap.Size(19, 31), // 图标大小
          imageSize: new AMap.Size(19, 31),
          image: 'https://webapi.amap.com/theme/v1.3/markers/b/start.png'
        })
      }
      const endMarkerOptions = {
        icon: new AMap.Icon({
          size: new AMap.Size(19, 31), // 图标大小
          imageSize: new AMap.Size(19, 31),
          image: 'https://webapi.amap.com/theme/v1.3/markers/b/end.png'
        }),
        offset: new AMap.Pixel(-9, -31)
      }
      const midMarkerOptions = {
        icon: new AMap.Icon({
          size: new AMap.Size(19, 31), // 图标大小
          imageSize: new AMap.Size(19, 31),
          image: 'https://webapi.amap.com/theme/v1.3/markers/b/mid.png'
        }),
        offset: new AMap.Pixel(-9, -31)
      }
      const lineOptions = {
        strokeStyle: 'solid',
        strokeColor: '#1593C9',
        strokeOpacity: 1,
        strokeWeight: 2
      }
      const rulerOptions = {
        startMarkerOptions: startMarkerOptions,
        midMarkerOptions: midMarkerOptions,
        endMarkerOptions: endMarkerOptions,
        lineOptions: lineOptions,
        zIndex: 999
      }
      if (this.isCj) {
        this.rulerTool.close(true)
        this.isCj = false
      } else {
        this.rulerTool.rule(rulerOptions)
        this.isCj = true
      }
    },
    lukuang() {
      if (this.isLk) {
        this.trafficLayer.hide()
        this.isLk = false
      } else {
        this.trafficLayer.show()
        this.isLk = true
      }
    },
    initMap() {
      var lnglat = new AMap.LngLat(116.397428, 39.90923)
      this.map = new AMap.Map('container', {
        center: lnglat, // [纬度，经度]
        resizeEnable: true,
        zoom: 10,
        mapStyle: 'amap://styles/91eb726d0146eff907afa40bf1b8104f'
      })
      const _this = this
      // 地形地图样式
      this.googleLayer = new AMap.TileLayer({
        zIndex: 2,
        getTileUrl: function(x, y, z) {
          return `https://t4.tianditu.gov.cn/DataServer?T=ter_w&x=${x}&y=${y}&l=${z}&tk=050f5cb6e9b8cc20a4e404ee29f9a366`
        }
      })
      this.map.plugin(
        [
          'AMap.RangingTool',
          'AMap.MouseTool',
          'AMap.TileLayer.Traffic',
          'AMap.TileLayer.Satellite',
          'AMap.ToolBar'
        ],
        function() {
          _this.map.addControl(new AMap.ToolBar())
          // 路况
          _this.trafficLayer = new AMap.TileLayer.Traffic({ zIndex: 10 })
          _this.trafficLayer.setMap(_this.map)
          // 测距
          _this.rulerTool = new AMap.MouseTool(_this.map)
          // 卫星地图
          _this.xmap = new AMap.TileLayer.Satellite({ map: _this.map })

          // 默认影藏
          _this.xmap.hide()
          // 路况记影藏
          _this.trafficLayer.hide()
        }
      )
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@media (max-width: 1024px) {
  .chart-wrapper {
    padding: 8px;
  }
}
.dashboard-container {
  position: relative;
}
#container {
  width: 100%;
  height: 100vh;
}
ul,
li {
  margin: 0;
  padding: 0;
  list-style: none;
}
ul {
  position: absolute;
  top: 10px;
  z-index: 999;
  right: 20px;
  padding: 14px 0 13px;
  background: #fff;
  border-radius: 3px;
  height: 45px;
}

li {
  display: inline-block;
  padding: 0 12px;
  font-size: 12px;
  border-left: 1px #dbdee2 dashed;
  vertical-align: middle;
  cursor: pointer;
  overflow: visible;
  zoom: 1;
  color: #5f6477;
}
li:first-child {
  border: none;
}
.active {
  color: rgb(11, 201, 175);
}
</style>
