<template>
  <div class="about">
    <div class="map-container">
      <div id="container" class="map-content"></div>
      <div id="panel" class="route-panel"></div>
    </div>
    
    <div class="control-box">
      <div 
        class="control-btn"
        v-for="item in dataList" 
        :key="item.name"
        @click="jump(item.value)"
        @mouseenter="hoverEffect(true)"
        @mouseleave="hoverEffect(false)"
      >
        <span class="btn-icon">📍</span>
        <span class="btn-text">{{ item.name }}</span>
      </div>
    </div>
  </div>
</template>
<script setup>
import { onMounted, ref } from 'vue'

const dataList = ref([
  { name: '中国华侨历史博物馆', value: [116.4250,39.9429] },
  { name: '中国国家博物馆', value: [116.4016,39.9051] },
  { name: '军事博物馆', value: [116.3236,39.9092] },
  { name: '北京古代建筑博物馆', value: [116.3925,39.8778] },
])
var map = null
var driving = null
var Amap2 = null
const getMap = () => {
  console.log('getMap');
  const AMap = window.AMap;
  // 创建地图实例
  map = new AMap.Map('container', {
    zoom: 11, // 地图显示的缩放级别
    center: [116.3974, 39.9086] // 地图中心点坐标
  });

  // 实例化DrivingSearch
  driving = new AMap.Driving({
    map: map,
    panel: "panel"
  });
  Amap2 = AMap;
}
const jump = (value) => {
  // 根据起终点经纬度规划驾车导航路线
  console.log('jump', value);
  let start = [116.3970,39.9177];
  let end = value;
  let tu = [116.4016,39.9051]; // 途径点
  driving.search(new window.AMap.LngLat(start[0], start[1]), new window.AMap.LngLat(end[0], end[1]), {
    waypoints: [new window.AMap.LngLat(tu[0], tu[1])]
  }, function (status, result) {
    // result即是对应的驾车导航信息
    console.log(status, result);
    if (status === 'complete') {
      console.log('路线规划成功');
    } else {
      console.log('路线规划失败：' + result.info);
    }
  });
}
onMounted(() => {
  getMap()
})

</script>

<style lang="less" scoped>
@primary-color: #1890ff;
@hover-color: #40a9ff;
@shadow-color: rgba(0, 0, 0, 0.1);

.about {
  padding: 24px;
  background: #f0f2f5;
  min-height: 100vh;
}

.map-container {
  display: grid;
  grid-template-columns: 1fr 300px;
  gap: 20px;
  max-width: 1200px;
  margin: 0 auto 24px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 4px 12px @shadow-color;
  overflow: hidden;
  
  .map-content {
    height: 600px;
    border-radius: 12px 0 0 12px;
  }
  
  .route-panel {
    padding: 16px;
    background: #fff;
    overflow-y: auto;
    border-left: 1px solid #eee;
    
    &::-webkit-scrollbar {
      width: 6px;
    }
    
    &::-webkit-scrollbar-thumb {
      background: #ddd;
      border-radius: 4px;
    }
  }
}

.control-box {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 16px;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;

  .control-btn {
    display: flex;
    align-items: center;
    padding: 14px 20px;
    background: linear-gradient(135deg, @primary-color, @hover-color);
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.3s ease;
    box-shadow: 0 2px 8px @shadow-color;
    
    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 4px 12px @shadow-color;
      
      .btn-icon {
        transform: scale(1.1);
      }
    }
    
    &:active {
      transform: translateY(0);
    }
    
    .btn-icon {
      font-size: 20px;
      margin-right: 12px;
      transition: transform 0.2s ease;
    }
    
    .btn-text {
      color: white;
      font-size: 15px;
      font-weight: 500;
      letter-spacing: 0.5px;
    }
  }
}

@media (max-width: 768px) {
  .map-container {
    grid-template-columns: 1fr;
    border-radius: 8px;
    
    .map-content {
      height: 400px;
      border-radius: 8px 8px 0 0;
    }
    
    .route-panel {
      height: 200px;
      border-left: none;
      border-top: 1px solid #eee;
    }
  }
  
  .control-box {
    grid-template-columns: 1fr;
  }
}
</style>
