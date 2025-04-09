<template>
  <div class="map-container">
    <div id="map" style="width: 100%; height: 600px;"></div>
    <div class="controls">
      <div class="control-group">
        <button 
          @click="toggleRightClick"
          :class="{ 'active': rightClickEnabled }"
        >
          {{ rightClickEnabled ? '关闭添加标记' : '开启添加标记' }}
        </button>
        <button @click="clearAllMarkers" class="danger">
          清除所有标记
        </button>
      </div>
      
      <div class="status-hint">
        <div class="status">
          <span class="label">当前标记数量：</span>
          <span class="value">{{ markers.length }}</span>
        </div>
        <div v-if="rightClickEnabled" class="operation-hint">
          🎯 操作提示：右键地图添加标记，左键点击标记删除
        </div>
      </div>
    </div>
  </div>
</template>
 
<script>
export default {
  data() {
    return {
      map: null,
      markers: [],
      rightClickEnabled: false,
      storageKey: "savedMarkers"
    };
  },
  methods: {
    initMap() {
      if (typeof AMap === 'undefined') {
        console.error('高德地图API未加载');
        return;
      }

      this.map = new AMap.Map('map', {
        resizeEnable: true,
        center: [116.397428, 39.90923],
        zoom: 13,
      });

      // 加载保存的标记
      this.loadMarkers();

      this.map.on('rightclick', (e) => {
        if (this.rightClickEnabled) {
          this.addMarker(e.lnglat);
        }
      });
    },

    toggleRightClick() {
      this.rightClickEnabled = !this.rightClickEnabled;
    },

    addMarker(position) {
      const marker = new AMap.Marker({
        position: position,
        icon: new AMap.Icon({
          size: new AMap.Size(25, 34),
          image: '/历史文化图标.png',
          imageSize: new AMap.Size(25, 25),
          offset: new AMap.Pixel(-12, -34)
        }),
        clickable: true // 确保标记可点击
      });
    
      marker.on('click', () => {
        this.removeMarker(marker);
      });

      marker.setMap(this.map);
      this.markers.push(marker);
      
      // 保存到本地存储
      this.saveMarkers();
    },

    removeMarker(marker) {
      // 修复：确保完全移除标记引用
      const index = this.markers.findIndex(m => m === marker);
      if (index > -1) {
        this.markers.splice(index, 1); // 使用数组变异方法确保响应式更新
        marker.setMap(null);
        this.saveMarkers();
      }
    },

    // 保存标记到本地存储
    saveMarkers() {
      const markerData = this.markers.map(marker => ({
        lng: marker.getPosition().getLng(),
        lat: marker.getPosition().getLat()
      }));
      localStorage.setItem(this.storageKey, JSON.stringify(markerData));
    },

    // 从本地存储加载标记
    loadMarkers() {
      const savedData = localStorage.getItem(this.storageKey);
      if (savedData) {
        try {
          const markers = JSON.parse(savedData);
          markers.forEach(pos => {
            this.addMarker(new AMap.LngLat(pos.lng, pos.lat));
          });
        } catch (e) {
          console.error('加载标记失败:', e);
        }
      }
    },

    // 清除所有标记
    clearAllMarkers() {
      if (confirm('确定要删除所有标记吗？')) {
        this.markers.forEach(marker => marker.setMap(null));
        this.markers = [];
        localStorage.removeItem(this.storageKey);
      }
    }
  },

  mounted() {
    this.initMap();
  },

  beforeDestroy() {
    this.markers.forEach(marker => marker.setMap(null));
  }
};
</script>

<style scoped>
/* 新增样式 */
.status-hint {
  margin-top: 12px;
  border-top: 1px solid #eee;
  padding-top: 8px;
}

.operation-hint {
  font-size: 12px;
  color: #666;
  margin-top: 6px;
  padding: 6px;
  background: #f8f8f8;
  border-radius: 4px;
}

.control-group {
  display: flex;
  gap: 8px;
}

.status {
  display: flex;
  align-items: center;
}

.label {
  color: #666;
}

.value {
  font-weight: bold;
  margin-left: 4px;
  color: #2196F3;
}
.map-container {
  position: relative;
  width: 100%;
  height: 600px;
}

#map {
  width: 100%;
  height: 100%;
}

.controls {
  position: absolute;
  top: 20px;
  left: 20px;
  z-index: 10;
  display: flex;
  gap: 10px;
  background: rgba(255, 255, 255, 0.9);
  padding: 15px;
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

button {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.3s ease;
}

button.active {
  background-color: #4CAF50;
  color: white;
}

button.danger {
  background-color: #ff4444;
  color: white;
}

button:hover {
  opacity: 0.9;
  transform: translateY(-1px);
}

.status {
  display: flex;
  align-items: center;
  padding: 0 12px;
  color: #666;
  font-size: 14px;
}
</style>
