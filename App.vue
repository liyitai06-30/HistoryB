<template>
  <div id="app" class="layout">
 <!-- 加载动画容器 -->
 <div v-if="isLoading" class="loading-overlay">
    <div class="loader-container">
      <!-- 双环旋转动画 -->
      <div class="double-ring">
        <div class="ring outer"></div>
        <div class="ring inner"></div>
      </div>
      <!-- 动态文字 -->
      <h1 class="loading-text">
        <span class="char" v-for="(char, index) in '京文京史'" :key="index" :style="charStyle(index)">
          {{ char }}
        </span>
      </h1>
    </div>
  </div>

    <!-- 顶部系统介绍 -->
    <header class="header">
      <h1>北京历史文化介绍</h1>
    </header>

    <div class="main-content">
      <!-- 左侧菜单栏 -->
      <nav class="sidebar">
        <ul>
          <li 
            v-for="item in menuItems" 
            :key="item.id" 
            @click="toggleMenu(item.id)"
            :class="{ activeMenu: activeMenuId === item.id }"
          >
            {{ item.name }}
            <!-- 子菜单 -->
            <transition name="slide-fade">
              <ul v-if="activeMenuId === item.id" class="sub-menu">
                <li
                  v-for="(subItem, index) in item.subMenu"
                  :key="index"
                  @click="handleSubMenuClick(subItem, item.id, index, $event)"
                  :class="{ activeSubMenu: isActiveSubMenu(item.id, index) }"
                >
                  {{ subItem }}
                </li>
              </ul>
            </transition>
          </li>
        </ul>
      </nav>

      <!-- 右侧地图部分 -->
      <section class="map-container">
   
  <div 
    v-if="activeMenuId === 1 && activeSubMenuId.index === null"
    class="default-prompt"
  >
    <h3>请选择要查看的地图分类</h3>
    <p>点击左侧菜单中的子项查看详细信息</p>
  </div>
        <!-- 显示不同地图内容 -->
        <MapComponent 
          v-if="activeMenuId === 1 && activeSubMenuId.index === 0" 
          :places="historySites"
        />
        <MapComponent 
          v-if="activeMenuId === 1 && activeSubMenuId.index === 1" 
          :places="culturalHeritage"
          @update:selectedHeritage="handleSelectedHeritage"
        />
        <MapComponent 
          v-if="activeMenuId === 1 && activeSubMenuId.index === 2" 
          :places="famousLandmarks"
        />
        <Markadd v-if="activeMenuId === 2" />

        <Guihua v-if="activeMenuId === 3 "/>

        <Mapsearch v-if="activeMenuId === 4"/>

         <!-- 功能5的容器 -->
         <div v-if="activeMenuId === 5" class="feature-container">
          <h2>北京历史文化介绍总览</h2>
          <p>这里是关于北京历史文化的详细介绍。通过以下的许多功能，将带你深入了解北京的历史遗址、文化遗产和名胜古迹。</p>

          <!-- 图片和文字左右排列 -->
          <div class="content-wrapper">
            <div class="text">
              <p>北京作为中国的首都，拥有丰富的历史文化遗址和深厚的文化底蕴。作为中国历史上多个朝代的都城，北京汇聚了众多反映古代文明的遗址与建筑，其中最具代表性的当属故宫、天坛、长城等。故宫作为明清两代的皇家宫殿，不仅是一座壮丽的宫殿群，更是中国古代建筑艺术与文化的象征。天坛是古代皇帝祭天的圣地，展示了中国古代的天人合一哲学思想和精湛的建筑技艺。而万里长城，则以其独特的军事防御功能和恢弘的规模，见证了中国古代的历史变迁。</p>
              <p>除了这些知名的历史遗址，北京还有许多独具魅力的文化遗产。恭王府、圆明园等是清代帝王的生活空间和文化艺术的集中体现，曾是皇家极尽奢华的象征。圆明园曾以“万园之园”闻名，承载着无数文化瑰宝，虽遭受劫难，但它依旧深深影响着后世的艺术和园林设计。京剧作为中国传统戏剧的代表，也诞生于北京，悠久的戏曲艺术和传统文化在这座城市里传承至今。此外，许多古老的胡同和四合院仍保留着传统的民居风貌，给人一种穿越时空的历史感。</p>
            </div>
            <div class="images">
              <img src="/故宫.jpg" alt="故宫" class="feature-image" />
              <img src="/天坛.jpg" alt="天坛" class="feature-image" />
            </div>
          </div>

          <div class="content-wrapper">
            <div class="text">
              <p>这些历史文化遗址和文化瑰宝，构成了北京丰富多彩的文化面貌，它们不仅是中华民族的宝贵遗产，也是世界文化的重要组成部分。游客在这里不仅能够欣赏到壮丽的建筑与遗址，还能感受到浓厚的文化气息，体验中国几千年文明的辉煌与深远影响。</p>
            </div>
            <div class="images">
              <img src="/颐和园.jpg" alt="颐和园" class="feature-image" />
              <img src="/明十三陵.jpg" alt="明十三陵" class="feature-image" />
            </div>
          </div>

          <!-- 视频部分 -->
          <video controls class="feature-video">
            <source src="/北京文化视频.mp4" type="video/mp4" />
            你的浏览器不支持视频播放。
          </video>
        </div>

      </section>
      
      <!-- 文化遗产展示板 -->
      <HistoryContent 
        :selectedHeritageId="selectedHeritageId" 
        :isVisible="isPanelVisible" 
        @update:isVisible="togglePanelVisibility"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const isLoading = ref(true)

// 文字动画延迟计算
const charStyle = computed(() => (index) => ({
  animationDelay: `${index * 0.1}s`
}))

onMounted(() => {
  setTimeout(() => {
    isLoading.value = false
  }, 4000)
})
</script>

<script>
import Guihua from "./components/guihua.vue";
import HistoryContent from "./components/HistoryContent.vue";
import MapComponent from "./components/mapcomponent.vue"; // 统一的地图组件
import Mapsearch from "./components/Mapsearch.vue";
import Markadd from "./components/Markadd.vue";
// 加载状态

export default {
  name: "App",
  components: {
    MapComponent,
    HistoryContent,
    Markadd,
    Guihua,
    Mapsearch
  },
  data() {
    return {
      activeMenuId: 5, 
      activeSubMenuId: { menuId: null, index: null }, // 默认不选择任何子菜单
      selectedHeritageId: null, // 初始为 null，表示没有选中任何文化遗产
      isPanelVisible: false, // 控制展示板的显示和隐藏，初始为隐藏
      menuItems: [
      { 
          id: 5, 
          name: "北京历史文化介绍总览", 
          subMenu: [] 
        },
        { 
          id: 1, 
          name: "遗址与古迹", 
          subMenu: ["历史遗址", "文化遗产", "名胜古迹"] 
        },
        { 
          id: 2, 
          name: "标记感兴趣历史文化点", 
        },
        { 
          id: 3, 
          name: "北京知名博物馆路线规划", 
        },
        { 
          id: 4, 
          name: "历史文化数据展示", 
        },
      ],
      // 北京的历史文化遗址
      historySites: [
        { 
          name: '故宫', 
          lat: 39.9163, 
          lng: 116.3972, 
          description: '故宫是中国明清两代的皇家宫殿，现为故宫博物院。', 
          image:  '/故宫.jpg'
        },
        { 
          name: '天坛', 
          lat: 39.8836, 
          lng: 116.4123, 
          description: '天坛是中国古代皇帝祭天的地方，是世界文化遗产。', 
          image: '/天坛.jpg' 
        },
        { 
          name: '颐和园', 
          lat: 39.9997, 
          lng: 116.2750, 
          description: '颐和园是中国清朝皇家园林，是世界文化遗产。', 
          image: '/颐和园.jpg' 
        },
        { 
          name: '明十三陵', 
          lat: 40.2884, 
          lng: 116.2205, 
          description: '明十三陵是中国明朝皇帝的陵墓，位于北京昌平。', 
          image: '/明十三陵.jpg' 
        }
      ],
      // 北京的文化遗产
      culturalHeritage: [
        { 
          id: 1, 
          name: '京剧', 
          lat: 39.9042, 
          lng: 116.4074, 
          description: '京剧是中国传统的戏剧形式之一，具有悠久的历史。', 
          image: '/京剧舞台.jpg' 
        },        
        { 
          id: 2,
          name: '长城', 
          lat: 40.4319, 
          lng: 116.5704, 
          description: '长城是中国古代防御工事，世界七大奇迹之一。', 
          image: '/长城.jpg' 
        },
        { 
          id: 3, 
          name: '天坛', 
          lat: 39.8836, 
          lng: 116.4123, 
          description: '天坛祭祠是中国古代皇帝祭天的仪式。', 
          image: '/天坛祭祀2.jpg' 
        },
        { 
          id: 4, 
          name: '北京四合院', 
          lat: 39.9375, 
          lng: 116.4085, 
          description: '四合院是传统的北京民居建筑形式，具有深厚的文化背景。', 
          image: '/北京四合院2.jpg' 
        },
        { 
          id: 5, 
          name: '紫禁城', 
          lat: 39.9163, 
          lng: 116.3972, 
          description: '紫禁城是明清两代的皇宫，现为故宫博物院。', 
          image: '/故宫博物院.jpg' 
        },
        { 
          id: 6, 
          name: '北京周口店遗址', 
          lat: 39.6880, 
          lng: 115.9284, 
          description: '人类化石宝库和古人类学、考古学、古生物学、地层学、年代学、环境学及岩溶学等多学科综合研究基地', 
          image: '/北京周口店遗址.jpg' 
        },
        { 
          id: 7, 
          name: '大运河', 
          lat: 39.9159, 
          lng: 116.67285, 
          description: '人类化石宝库和古人类学、考古学、古生物学、地层学、年代学、环境学及岩溶学等多学科综合研究基地', 
          image: '/北京大运河.jpg' 
        },
        { 
          id:8,
          name: '明十三陵', 
          lat: 40.2884, 
          lng: 116.2205, 
          description: '明十三陵是中国明朝皇帝的陵墓，位于北京昌平。', 
          image: '/明十三陵.jpg' 
        }
      ],
      // 北京的名胜古迹
      famousLandmarks: [
        { 
          name: '天安门',
          lat: 39.9037, 
          lng: 116.3974, 
          description: '天安门是中国的象征，位于北京市中心。', 
          image: '/天安门.jpg' 
        },
        { 
          name: '圆明园', 
          lat: 39.9996, 
          lng: 116.31019, 
          description: '圆明园始建于1707年，占地面积3.5平方千米，建筑面积达20万平方米，150余景，有“万园之园”之称。', 
          image: '/圆明园.jpg' 
        },
        { 
          name: '恭王府', 
          lat: 39.9372, 
          lng: 116.3863, 
          description: '恭王府是我国保存较为完整的王府建筑群，曾先后作为和珅、永璘的宅邸，1851年恭亲王奕訢成为宅子的主人，恭王府的名称也因此得来。', 
          image: '/恭王府.jpg' 
        },
        { 
          name: '北海公园', 
          lat: 39.9262, 
          lng: 116.3882, 
          description: '北海公园位于北京市中心区，城内景山西侧，故宫的西北面，与中海、南海合称三海，是中国古代皇家园林。', 
          image: '/北海公园.jpg' 
        },
        { 
          name: '地坛公园', 
          lat: 39.9542, 
          lng: 116.4154, 
          description: '地坛（Altar of Earth），又称方泽坛，位于北京市东城区安定门外大街地坛公园内，与天坛遥相对应', 
          image: '/地坛公园.jpg' 
        },
        { 
          name: '紫竹院', 
          lat: 39.9445, 
          lng: 116.3119, 
          description: '紫竹院公园，位于北京市海淀区中关村南大街35号，因园内西北部有明清时期庙宇“福荫紫竹院”而得名。', 
          image: '/紫竹院.jpg' 
        },
        // 其他名胜古迹...
      ],
    };
  },
  methods: {
    toggleMenu(id) {
      this.activeMenuId = this.activeMenuId === id ? null : id;
      if (this.activeMenuId === null) {
        this.activeSubMenuId = { menuId: null, index: null };
      }
    },
    handleSubMenuClick(subItem, menuId, index, event) {
       event.stopPropagation();
        this.activeSubMenuId = { menuId, index };
      
      // 只有点击到“文化遗产”时，才显示相关展示板
        if (subItem === '文化遗产') {
        this.selectedHeritageId = null; // 初始不显示任何文化遗产
        this.isPanelVisible = false; // 初始时隐藏展示板
        } else {
        this.isPanelVisible = false;
        }
    },

    isActiveSubMenu(menuId, index) {
      return this.activeSubMenuId.menuId === menuId && this.activeSubMenuId.index === index;
    },

    handleSelectedHeritage(place) {
      this.selectedHeritageId = place.id; 
      this.isPanelVisible = true; // 显示展示板
    },

    togglePanelVisibility(visible) {
      this.isPanelVisible = visible;
    },

    handleSearchResult(result) {
      console.log('搜索结果:', result);
      // 处理搜索结果，更新相关数据或执行其他操作
    },
  }
};
</script>


<style scoped>
.layout {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.header {
  background: linear-gradient(
    135deg,
    rgba(12, 36, 97, 0.95) 0%,
    rgba(45, 20, 44, 0.95) 50%,
    rgba(36, 62, 54, 0.95) 100%
  );
  color: #fff;
  text-align: center;
  padding: 15px 0;
  position: relative;
  overflow: hidden;
  backdrop-filter: blur(5px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.15);
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.3);
}

/* 添加流动光效 */
.header::after {
  content: "";
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: linear-gradient(
    45deg,
    transparent 25%,
    rgba(255, 255, 255, 0.1) 50%,
    transparent 75%
  );
  animation: lightFlow 6s linear infinite;
  transform: rotate(30deg);
}

@keyframes lightFlow {
  0% { transform: translateY(-50%) rotate(30deg) }
  100% { transform: translateY(50%) rotate(30deg) }
}

/* 文字特效 */
.header h1 {
  position: relative;
  z-index: 2;
  font-size: 2.2rem;
  text-shadow: 
    0 0 10px rgba(255, 255, 255, 0.3),
    0 0 20px rgba(231, 76, 60, 0.5),
    0 0 30px rgba(46, 204, 113, 0.3);
  animation: titleGlow 2s ease-in-out infinite alternate;
}

@keyframes titleGlow {
  from { opacity: 0.95; }
  to { opacity: 1; }
}

.main-content {
  display: flex;
  flex: 1;
}

.sidebar {
  width: 200px;
  background-color: #f4f4f4;
  padding: 10px;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
}

.sidebar ul {
  list-style: none;
  padding: 0;
}

.sidebar li {
  padding: 10px;
  cursor: pointer;
  transition: background-color 0.3s;
}

/* 功能项被选中时的样式 */
.sidebar li.activeMenu {
  background-color: #e0e0e0;
}

/* 子菜单项的样式 */
.sub-menu {
  list-style: none;
  padding-left: 20px;
}

.sub-menu li {
  padding: 8px 0;
  cursor: pointer;
  transition: background-color 0.3s;
}

.sub-menu li:hover {
  background-color: #e0e0e0;
}

.sub-menu li.activeSubMenu {
  background-color: #4CAF50; /* 被点击后的高亮颜色 */
  color: white;
}

.map-container {
  flex: 1;
  position: relative;
}

/* 子菜单展开的平滑过渡动画 */
.slide-fade-enter-active, .slide-fade-leave-active {
  transition: all 0.3s ease;
}

.slide-fade-enter, .slide-fade-leave-to {
  transform: translateY(-10px);
  opacity: 0;
}
/* 功能5容器的样式 */
.feature-container {
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  width: 80%;
  max-width: 1200px;
  margin: 20px auto;
}

.feature-image {
  width: 100%;
  max-width: 300px; /* 控制每张图片的最大宽度 */
  height: auto;
  margin-bottom: 10px;
}


/* 图片和文字左右排列 */
.content-wrapper {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

.content-wrapper .text {
  width: 48%;
}

.content-wrapper .images {
  width: 48%;
}

.feature-video {
  width: 100%;
  height: auto;
  margin-top: 20px;
}
/* 加载动画样式 */
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(45deg, #2c3e50, #34495e);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
  backdrop-filter: blur(5px);
}

.loader-container {
  text-align: center;
}

.double-ring {
  position: relative;
  width: 120px;
  height: 120px;
  margin: 0 auto 30px;
}

.ring {
  position: absolute;
  border-radius: 50%;
  border: 6px solid transparent;
  animation: rotate 4s linear infinite;
}

.outer {
  width: 100%;
  height: 100%;
  border-top-color: #e73c29;
  border-bottom-color: #3498db;
}

.inner {
  width: 80%;
  height: 80%;
  top: 10%;
  left: 10%;
  border-left-color: #2ecc71;
  border-right-color: #f1c40f;
  animation-duration: 1.5s;
  animation-direction: reverse;
}

@keyframes rotate {
  0% { transform: rotate(0deg) }
  100% { transform: rotate(360deg) }
}

.loading-text {
  font-family: 'Microsoft YaHei', sans-serif;
  font-size: 3rem;
  color: rgba(255, 255, 255, 0.9);
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.3);
}

.char {
  display: inline-block;
  opacity: 0;
  animation: charPop 0.2s ease forwards;
}

@keyframes charPop {
  0% {
    opacity: 0;
    transform: translateY(20px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 页面加载后的过渡效果 */
.layout {
  animation: pageFadeIn 0.5s ease;
}

@keyframes pageFadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.default-prompt {
  padding: 2rem;
  text-align: center;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  animation: fadeIn 0.5s ease;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
</style>
