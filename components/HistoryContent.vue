<template>
  <div 
    id="culture-heritage-panel" 
    :class="{'content-wrapper': true, 'show': isVisible}" 
    class="content-wrapper"
  >
    <button @click="closePanel" class="close-btn">关闭</button>

    <div v-if="selectedHeritage" class="heritage-content">
      <h2>{{ selectedHeritage.name }}</h2>
      <div class="heritage-image">
        <img :src="selectedHeritage.image" :alt="selectedHeritage.name" />
      </div>
      <p v-html="selectedHeritage.description"></p> <!-- 使用 v-html 渲染 HTML 内容 -->
    </div>
  </div>
</template>

<script>
export default {
  props: {
    selectedHeritageId: Number, // 传递选中的文化遗产 ID
    isVisible: Boolean, // 控制面板是否可见
  },
  data() {
    return {
      culturalHeritageList: [
        {
          id: 1,
          name: '京剧',
          description: '京剧，作为中国传统的戏曲艺术之一，是中国最具代表性和影响力的戏剧形式之一。它源于北京，融合了各地方的戏曲风格，逐渐发展成为中国的国粹之一。京剧的特点不仅仅体现在其音乐、表演、舞美等方面，更在于它所蕴含的深厚文化底蕴<br>'
          +'京剧的历史可以追溯到18世纪中期清朝乾隆年间。当时，徽剧和汉剧等地方戏曲的元素传入北京，经过长期的融合和发展，形成了京剧的雏形。清朝中期，京剧逐渐得到宫廷的支持，尤其是乾隆帝的推崇，进一步推动了它的普及。到了19世纪中期，京剧已成为中国最主要的戏曲形式，并开始在全国范围内传播',
          image: '/京剧.jpg',
        },
        {
          id: 2,
          name: '长城',
          description: '长城不仅是中国也是世界上修建时间最长、工程量最大的一项古代防御工程，自西周时期开始，延续不断修筑了2000多年，分布于中国北部和中部的广大土地上，总计长度达2万多千米<br>'
          +'由于年代久远，早期各个朝代的长城大多数都残缺不全，保存得比较完整的是明代修建的长城，所以人们一般说的长城指的是明长城，所称长城的长度，也就是明长城的长度。',
          image: '/长城2.jpg',
        },
        {
          id: 3,
          name: '天坛',
          description: '天坛位于北京，是明清两代皇帝祭天祈谷的重要场所，象征着中国古代封建社会对天地自然的崇敬与依赖。天坛的祭祀活动通常在冬至前后举行，皇帝亲自前往祭天，祈求国家安泰、五谷丰登。祭祀仪式包括祭天、祈雨、献牲等环节，场地布局严谨，体现了天人合一的哲学思想。<br>'
          +'天坛建筑群包括祈年殿、圆丘、皇穹宇等，均具有象征意义。祈年殿是祭天的主场，圆丘是皇帝祭天时的祭坛，而皇穹宇则存放着天神的牌位。天坛不仅是祭祀的场所，也是中国古代宇宙观和宗教文化的重要象征。它的祭祀活动深刻体现了中国古代帝王对自然、对神灵的敬畏与崇拜',
          image: '/天坛祭祀.jpg',
        },
        {
          id: 4,
          name: '北京四合院',
          description: '  北京四合院是传统的北京民居形式，具有深厚的历史文化底蕴。四合院通常由四面房屋围成一个庭院，呈“口”字形，四面房屋分别为正房、厢房和倒座房。院落中央通常有一块小花园或空地，充满传统的中国文化气息。<br>'
          +'  四合院的设计讲究对称、合理，体现了“天人合一”的哲学思想。院落围合的结构不仅能保护隐私，还能通过风水布局实现居住舒适度和家族和谐。正房一般位于院子的北侧，是家中最重要的位置，主要用于接待贵客和家庭活动；厢房通常位于东西两侧，作为家庭成员的卧室或储物间；倒座房则常用作厨房或杂物间。<br>'
          +'  四合院不仅是居住空间，也承载着家族的历史与文化。许多四合院还注重细节装饰，门窗、院墙等地方常见雕刻、绘画等艺术形式，展示了中国传统建筑的精湛技艺。<br>'
          +'  然而，随着现代化进程的推进，许多传统四合院面临拆迁或改建，成为现代都市建设中的一部分。尽管如此，四合院依然是北京乃至中国传统文化和建筑的重要象征。',
          image: '/北京四合院.jpg',
        },
        {
          id: 5,
          name: '紫禁城',
          description: '紫禁城是中国古代宫殿建筑的杰出代表，位于北京市中心，是明清两代皇帝的皇宫，也是世界上保存最完整、规模最大的古代宫殿建筑群之一。<br>它建于明朝永乐年间（1406年-1420年），历时14年建成。紫禁城不仅是中国古代帝王的政治中心和居住地，也是中华文化的重要象征',
          image: '/故宫.jpg',
        },
        {
          id: 6,
          name: '北京周口店遗址',
          description: '周口店遗址（Zhoukoudian Site），又称“周口店北京人遗址”，位于北京市房山区周口店镇龙骨山，诞生于约三至七十万年前，是世界范围内更新世古人类遗址中内涵较丰富、材料较齐全、较有科研价值的遗址之一<br>'
          +'遗址保护范围1368公顷，共发现不同时期的各类化石和文化遗物地点27处，出土人类化石200余件，石器10多万件以及大量的用火遗迹及上百种动物化石等，是人类化石宝库和古人类学、考古学、古生物学、地层学、年代学、环境学及岩溶学等多学科综合研究基地<br>'
          +'1961年3月4日，周口店遗址被中华人民共和国国务院公布为第一批全国重点文物保护单位。1987年，周口店遗址被公布为世界文化遗产 。2021年10月18日，周口店遗址入选“百年百大考古发现” 。2023年5月，北京市文物局公布《周口店遗址保护规划（2021-2035年）',
          image: '/周口店遗址.jpg',
        },
        { 
          id: 7, 
          name: '大运河', 
          lat: 39.9159, 
          lng: 116.67285, 
          description: '北京大运河是中国古代最重要的人工水道之一，也是世界文化遗产之一，起源于公元前5世纪，历时千年建设，最终连接了中国北方的京杭大运河。大运河全长约1794公里，贯穿了北京、天津、河北、山东等多个省份，是中国历史上最具规模的运河系统之一。<br>'
          +'北京大运河段自唐朝开始逐步完善，至元朝时期达到了最盛大的工程规模。运河贯穿了北京的城区，成为连接南北的主要水运通道，也促进了南方与北方的经济、文化交流。特别是在明清时期，大运河不仅是物资运输的重要通道，还促进了北京市的文化繁荣和经济发展。<br>'
          ,
          image: '/大运河2.jpg' 
        },
        { 
          id:8,
          name: '明十三陵', 
          lat: 40.2884, 
          lng: 116.2205, 
          description: '明十三陵是明朝皇帝的皇家陵寝群，位于北京市昌平区的天寿山，是中国古代帝王陵墓的代表之一。它是明朝13位皇帝的葬地，因此得名“十三陵”。这些陵墓不仅规模宏大，且设计精妙，是中国封建社会葬制和皇家文化的重要体现。'
          +'1995年，明十三陵被列为世界文化遗产，它作为中国皇家陵寝的代表，不仅在中国历史上具有重要地位，也是世界文明史上一笔宝贵的文化财富。<br>'
          +'总的来说，明十三陵是一座具有极高历史价值、艺术价值与文化价值的皇家陵寝，它见证了明朝的辉煌与兴衰，也承载着古代中国深厚的传统文化。', 
          image: '/明十三陵2.jpg' 
        }
      ],
      selectedHeritage: null,
    };
  },
  methods: {
    selectHeritage(id) {
      this.selectedHeritage = this.culturalHeritageList.find(
        (heritage) => heritage.id === id
      );
    },
    closePanel() {
      this.$emit('update:isVisible', false);
    },
  },
  watch: {
    selectedHeritageId(newId) {
      this.selectHeritage(newId);
    },
  },
  created() {
    // 在组件初始化时通过 selectedHeritageId 更新 selectedHeritage
    this.selectHeritage(this.selectedHeritageId);
  },
};
</script>

<style scoped>
.content-wrapper {
  position: fixed;
  top: 0;
  right: 0;
  width: 350px;
  height: 100%;
  background-color: white;
  box-shadow: -2px 0 10px rgba(0, 0, 0, 0.3);
  z-index: 999;
  overflow-y: auto;
  padding: 20px;
  transform: translateX(100%); /* 初始位置 */
  transition: transform 0.3s ease-in-out; /* 控制滑入的动画 */
}

.content-wrapper.show {
  transform: translateX(0); /* 面板滑入 */
}

.heritage-content {
  padding: 20px;
}

.heritage-image img {
  width: 100%;
  height: auto;
  margin: 20px 0;
}

.close-btn {
  position: absolute;
  top: 20px;
  right: 20px;
  background-color: red;
  color: white;
  border: none;
  padding: 10px;
  cursor: pointer;
  border-radius: 5px;
}

.close-btn:hover {
  background-color: darkred;
}
/* 每个段落首行缩进 */
.heritage-content p {
  text-indent: 2em; /* 设置每个段落首行缩进2个字符 */
  margin-bottom: 1em; /* 设置段落之间的间隔 */
  white-space: pre-wrap; /* 保持文本的格式，允许换行 */
}

/* 可选：控制段落之间的间隔 */
.heritage-content p + p {
  margin-top: 1em; /* 设置每个段落之间的间距 */
}
</style>
