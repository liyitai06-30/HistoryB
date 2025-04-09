<template>
  <div id="map" class="map"></div>
</template>

<script>
export default {
  name: "MapComponent",
  props: {
    places: Array,
    enableSearch: {
      type: Boolean,
      required: true,
    },
  },
  mounted() {
    this.initMap();
  },
  methods: {
    initMap() {
     
      // 初始化地图
      const map = new AMap.Map("map", {
        zoom: 9,
        center: [116.397428, 39.90923], // 初始视图：北京天安门
      });
      // 检查地图是否加载成功
      console.log("高德地图已加载");



      if (this.enableSearch) {
            this.initSearch(map, AMap)  // 启用搜索框功能
          }




      // 创建一个InfoWindow实例，用于显示弹框
      const infoWindow = new AMap.InfoWindow({
        isCustom: true,  // 使用自定义窗体
        offset: new AMap.Pixel(0, -30),  // 弹框相对标记的偏移
      });


      // 检查places数据是否正确传递
      if (!this.places || this.places.length === 0) {
        console.log("没有地点数据");
        return;
      }
    
    
      // 根据传递的地点数据，在地图上添加标记
      this.places.forEach((place) => {
        const marker = new AMap.Marker({
          position: new AMap.LngLat(place.lng, place.lat),
          title: place.name,  // 鼠标悬停时显示名称
        });
      
        // 添加标记到地图
        marker.setMap(map);

        // 点击标记时显示详细信息
        marker.on("click", () => {
          // 设置合适的缩放级别，您可以根据需要调整
          const content = this.createInfoWindowContent(place);
          infoWindow.setContent(content);  // 设置弹框内容
          infoWindow.open(map, marker.getPosition());  // 打开弹框
          map.setCenter(new AMap.LngLat(place.lng, place.lat));  // 设置地图中心为点击标记的位置
          map.setZoom(13);  // 设置合适的缩放级别，您可以根据需要调整
          this.$emit('update:selectedHeritage', place);
        });
      });
      
      // 添加工具栏
      map.plugin("AMap.ToolBar", () => {
        const toolBar = new AMap.ToolBar({
          position: "RT", // 设置位置为右上角
          offset: new AMap.Pixel(-20, 20), // 设置位置偏移
        });
        map.addControl(toolBar);
      });

      // 添加比例尺
      map.plugin("AMap.Scale", () => {
        const scale = new AMap.Scale();
        map.addControl(scale);
      });

      // 添加鹰眼视图
      map.plugin("AMap.HawkEye", () => {
        const hawkEye = new AMap.HawkEye({
          open: true, // 默认打开鹰眼
        });
        map.addControl(hawkEye);
      });
    },
  
    // 初始化搜索框
    initSearch(map, AMap) {

      const autoOptions = {
        input: 'tipinput'  // 输入框ID
      }

      AMap.plugin(['AMap.PlaceSearch', 'AMap.AutoComplete'], () => {
     
      const auto = new AMap.AutoComplete({
        input:autoOptions,
        output:'outPutBox'
   
      })
     // 自动完成搜索框
        const placeSearch = new AMap.PlaceSearch({
          map: map  // 地图实例
        });

        AMap.event.addListener(auto, 'select', (e) => {
          placeSearch.setCity(e.poi.adcode);  // 设置当前城市
          placeSearch.search(e.poi.name);  // 关键字查询
          this.map.setCenter(e.poi.location);  // 定位到选中的地点
          this.map.setZoom(13);  // 设置合适的缩放级别
        });
      })
    },
    // 创建自定义的弹框内容
    createInfoWindowContent(place) {
      return `
        <div style="width: 200px;">
          <h3 style="margin: 0; padding: 5px 0; font-size: 16px;">${place.name}</h3>
          <div>
            <p style="font-size: 14px; margin: 5px 0;">${place.description}</p>
            <img src="${place.image}" alt="${place.name}" style="width: 100%; border-radius: 4px;" />
          </div>
        </div>
      `;
    },
  },
};
</script>

<style lang="scss" scoped>
.map {
  width: 100%;
  height: 100%;

}
.search {
    position: absolute;
    top: 2%;
    left: 2%;
    z-index: 99;
  }
#tipinput {
  width: 100%;
  padding: 5px;
  font-size: 14px;
  }

</style>
