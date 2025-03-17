<template>
  <div class="article-container donate-container">
    <div class="article-header">
      <i class="el-icon-reading"></i>
      <span>科普文章</span>
      <div class="header-divider"></div>
    </div>
    
    <div class="article-content">
      <!-- 文章列表 -->
      <div v-if="loading" class="loading-container">
        <el-skeleton :rows="10" animated />
      </div>
      
      <div v-else-if="tableData.length === 0" class="empty-container">
        <el-empty description="暂无科普文章" :image-size="200">
          <el-button v-if="user.id" type="primary" @click="handleAdd">发布文章</el-button>
        </el-empty>
      </div>
      
      <div v-else class="article-list">
        <el-row :gutter="30">
          <el-col :xs="24" :sm="12" :md="8" :lg="6" v-for="item in tableData" :key="item.id">
            <el-card class="article-card" shadow="hover" @click.native="view(item.id, item.content)">
              <div class="article-card-header">
                <el-tag size="small" type="success" class="article-category">{{ getCategoryName(item.category) }}</el-tag>
                <span class="article-views"><i class="el-icon-view"></i> {{ item.views || 0 }}</span>
              </div>
              <h3 class="article-title">{{ item.name }}</h3>
              <div class="article-card-content">
                <div class="article-preview">{{ getPreviewContent(item.content) }}</div>
              </div>
              <div class="article-card-footer">
                <div class="article-info">
                  <span class="article-author"><i class="el-icon-user"></i> {{ item.user }}</span>
                  <span class="article-time"><i class="el-icon-time"></i> {{ item.time }}</span>
                </div>
                <div class="article-actions" @click.stop>
                  <el-button 
                    v-if="item.userId === user.id" 
                    type="text" 
                    icon="el-icon-delete" 
                    class="delete-btn" 
                    @click.stop="del(item.id)">删除</el-button>
                  <el-button 
                    v-if="item.userId === user.id" 
                    type="text" 
                    icon="el-icon-edit" 
                    @click.stop="handleEdit(item)">编辑</el-button>
                </div>
              </div>
            </el-card>
          </el-col>
        </el-row>
      </div>
      
      <!-- 分页 -->
      <div class="pagination-container" v-if="total > pageSize">
        <el-pagination
          @current-change="handleCurrentChange"
          :current-page="pageNum"
          :page-size="pageSize"
          background
          layout="prev, pager, next"
          :total="total">
        </el-pagination>
      </div>
      
      <!-- 悬浮按钮 -->
      <el-button 
        v-if="user.id" 
        class="floating-btn" 
        type="primary" 
        icon="el-icon-plus" 
        circle 
        @click="handleAdd">
      </el-button>
    </div>
    
    <!-- 文章编辑对话框 -->
    <el-dialog 
      :title="form.id ? '编辑文章' : '发布文章'" 
      :visible.sync="dialogFormVisible" 
      width="70%" 
      :close-on-click-modal="false"
      :destroy-on-close="true">
      <el-form :model="form" :rules="rules" ref="articleForm" label-width="80px" size="small">
        <el-form-item label="标题" prop="name">
          <el-input v-model="form.name" placeholder="请输入文章标题"></el-input>
        </el-form-item>
        <el-form-item label="分类" prop="category">
          <el-select v-model="form.category" placeholder="请选择文章分类" style="width: 100%">
            <el-option label="宠物健康" value="health"></el-option>
            <el-option label="行为训练" value="training"></el-option>
            <el-option label="饲养知识" value="care"></el-option>
            <el-option label="动物保护" value="protection"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="内容" prop="content">
          <mavon-editor 
            ref="md" 
            v-model="form.content" 
            :ishljs="true" 
            @imgAdd="imgAdd"
            style="min-height: 400px"
            placeholder="请输入文章内容，支持 Markdown 格式">
          </mavon-editor>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="save">发 布</el-button>
      </div>
    </el-dialog>
    
    <!-- 文章查看对话框 -->
    <el-dialog 
      :title="currentArticle.name" 
      :visible.sync="vis" 
      width="80%" 
      :close-on-click-modal="true"
      class="article-view-dialog"
      :fullscreen="false">
      <div class="article-view-header">
        <div class="article-view-info">
          <el-tag size="medium" type="success">{{ getCategoryName(currentArticle.category) }}</el-tag>
          <span class="article-view-author"><i class="el-icon-user"></i> {{ currentArticle.user }}</span>
          <span class="article-view-time"><i class="el-icon-time"></i> {{ currentArticle.time }}</span>
          <span class="article-view-views"><i class="el-icon-view"></i> {{ currentArticle.views || 0 }} 阅读</span>
        </div>
        <div class="article-view-actions" v-if="currentArticle.userId === user.id">
          <el-button type="primary" size="small" icon="el-icon-edit" @click="handleEdit(currentArticle)">编辑</el-button>
          <el-button type="danger" size="small" icon="el-icon-delete" @click="del(currentArticle.id)">删除</el-button>
        </div>
      </div>
      <div class="article-view-content">
        <mavon-editor
          class="md"
          :value="content"
          :subfield="false"
          :defaultOpen="'preview'"
          :toolbarsFlag="false"
          :editable="false"
          :scrollStyle="true"
          :ishljs="true"
        />
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { fixImageUrl } from '@/utils/request'

export default {
  name: "ArticleKp",
  data() {
    return {
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: 12,
      loading: true,
      form: {
        category: ""
      },
      rules: {
        name: [
          { required: true, message: '请输入文章标题', trigger: 'blur' },
          { min: 2, max: 100, message: '长度在 2 到 100 个字符', trigger: 'blur' }
        ],
        category: [
          { required: true, message: '请选择文章分类', trigger: 'change' }
        ],
        content: [
          { required: true, message: '请输入文章内容', trigger: 'blur' }
        ]
      },
      dialogFormVisible: false,
      vis: false,
      content: "",
      currentArticle: {},
      user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {}
    }
  },
  created() {
    this.load()
  },
  methods: {
    fixImageUrl,
    load() {
      this.loading = true
      this.request.get("/articlekp/page", {
        params: {
          pageNum: this.pageNum,
          pageSize: this.pageSize
        }
      }).then(res => {
        this.tableData = res.data.records
        this.total = res.data.total
        this.loading = false
        
        // 如果没有文章数据，加载预设文章
        if (this.tableData.length === 0) {
          this.loadSampleArticles()
        }
      }).catch(() => {
        this.loading = false
        // 加载失败时，显示预设文章
        this.loadSampleArticles()
      })
    },
    
    // 加载预设的示例文章
    loadSampleArticles() {
      const currentTime = new Date().toLocaleDateString()
      this.tableData = [
        {
          id: 'sample-1',
          name: '如何正确给猫咪洗澡 - 专业指南',
          category: 'care',
          content: `# 如何正确给猫咪洗澡 - 专业指南

## 为什么猫咪需要洗澡？

虽然猫咪通常能够自己保持清洁，但在某些情况下，它们可能需要我们的帮助：

* 猫咪毛发过脏或沾染了难以自行清洁的物质
* 特殊品种的猫需要定期护理
* 老年或肥胖的猫可能无法有效地自我清洁
* 猫有皮肤问题需要药浴

## 洗澡前的准备工作

1. **选择合适的时间**：选择猫咪平静的时候，避开它刚吃完或正在睡觉的时间。
2. **准备必要的用品**：
   - 猫咪专用洗发水（不要使用人用洗发水）
   - 防滑垫
   - 多条毛巾
   - 梳子
   - 猫咪喜欢的玩具或零食作为奖励

## 洗澡步骤

1. **梳理毛发**：先梳理猫咪的毛发，去除任何打结和松散的毛发。
2. **准备温水**：水温应与猫咪体温相近，约38°C左右。水位不宜过高，以免猫咪感到恐惧。
3. **轻柔地放入猫咪**：用一只手扶住猫咪的肩膀，另一只手托住后腿，缓慢地将猫咪放入水中。
4. **从颈部开始**：从猫咪的颈部开始，慢慢向后冲洗，注意避开眼睛、耳朵和鼻子。
5. **使用洗发水**：适量使用猫咪专用洗发水，轻轻按摩揉搓。
6. **彻底冲洗**：确保所有洗发水都被冲洗干净，残留的洗发水可能导致皮肤问题。
7. **擦干**：用毛巾包裹猫咪，轻轻吸干大部分水分。
8. **保持温暖**：确保猫咪在完全干燥前待在温暖的环境中，避免感冒。

## 洗澡后的护理

1. **梳理**：等猫咪毛发半干时，再次梳理以防打结。
2. **奖励**：给予猫咪喜欢的零食或玩具，建立正面联系。
3. **定期护理**：建立规律的梳理习惯，减少需要洗澡的频率。

记住，大多数健康的成年猫每年只需要洗几次澡。过度洗澡会去除猫咪皮肤上的天然油脂，导致皮肤干燥和刺激。`,
          user: '宠物护理专家',
          userId: 'sample-user',
          time: currentTime,
          views: 125
        },
        {
          id: 'sample-2',
          name: '狗狗的常见行为问题及解决方法',
          category: 'training',
          content: `# 狗狗的常见行为问题及解决方法

## 吠叫过度

### 问题描述
过度吠叫是犬类最常见的行为问题之一，可能因为警戒、恐惧、无聊或寻求注意力而发生。

### 解决方法
1. **找出原因**：观察什么情况下狗狗会吠叫，以及它吠叫时的肢体语言。
2. **消除刺激**：如果是对外界刺激的反应，尝试减少狗狗接触这些刺激的机会。
3. **正面强化**：当狗狗安静时给予奖励，教它"安静"的指令。
4. **充分运动**：确保狗狗有足够的身体和心理刺激，避免因无聊而吠叫。
5. **专业帮助**：如果问题严重，考虑咨询兽医行为学家。

## 乱咬物品

### 问题描述
狗狗，特别是幼犬，常常会咬家具、鞋子或其他家庭物品。

### 解决方法
1. **提供替代品**：确保狗狗有足够的适合咀嚼的玩具。
2. **防止接触**：将贵重或危险的物品放在狗狗够不到的地方。
3. **苦味剂**：在不能移动的物品上使用安全的苦味喷剂。
4. **监督和转移**：当发现狗狗开始咬不适合的物品时，立即转移它的注意力。
5. **足够运动**：确保狗狗有充分的运动和精神刺激。

## 分离焦虑

### 问题描述
当与主人分离时，狗狗表现出极度焦虑，可能会吠叫、破坏性行为、不适当排泄或过度舔舐。

### 解决方法
1. **逐渐习惯**：训练狗狗习惯短时间的独处，然后逐渐延长时间。
2. **离开信号**：避免在离开前给予过多关注，减少离开和回来的"戏剧性"。
3. **舒适环境**：创造安全、舒适的环境，可以留下带有你气味的物品。
4. **玩具分散注意力**：使用互动玩具或填充了零食的Kong玩具。
5. **药物治疗**：严重情况下，在兽医指导下使用抗焦虑药物。

## 跳上人或家具

### 问题描述
狗狗因兴奋或寻求注意力而跳到人身上或家具上。

### 解决方法
1. **忽略不良行为**：当狗狗跳起来时，转身离开，不给予任何注意。
2. **奖励好行为**：当狗狗四脚着地时给予奖励和关注。
3. **一致性**：确保家里所有人都遵循相同的规则。
4. **提供替代行为**：教狗狙"坐下"或"躺下"等指令作为获得注意的方式。

## 护食或玩具攻击性

### 问题描述
狗狗在食物或玩具附近表现出攻击性，包括咆哮、露齿或咬人。

### 解决方法
1. **资源交换**：训练狗狗交换物品，给予更有价值的奖励。
2. **多碗喂食**：如果有多只宠物，使用多个喂食区域减少竞争。
3. **建立规则**：通过训练建立清晰的规则和界限。
4. **寻求专业帮助**：护食攻击性可能很危险，严重情况下请咨询兽医行为学家。

记住，解决行为问题需要时间和耐心。保持一致性，使用正面强化而不是惩罚，将创造更健康、更和谐的主宠关系。`,
          user: '犬类行为专家',
          userId: 'sample-user',
          time: currentTime,
          views: 98
        },
        {
          id: 'sample-3',
          name: '宠物鸟的饲养基础知识',
          category: 'care',
          content: `# 宠物鸟的饲养基础知识

## 选择合适的宠物鸟

选择宠物鸟时，应考虑以下因素：

* **寿命**：一些鹦鹉品种可以活50年以上，这是一个长期承诺
* **噪音水平**：有些鸟类非常喜欢发出声音，可能不适合公寓生活
* **互动需求**：鹦鹉等社交性鸟类需要大量互动和注意力
* **空间需求**：大型鸟类需要更大的笼子和活动空间
* **初学者友好度**：金丝雀、虎皮鹦鹉和爱情鸟通常适合初次养鸟的人

## 基本设备需求

### 鸟笼

* **尺寸**：应该足够大，让鸟可以完全展开翅膀而不碰到任何东西
* **材质**：不含铅、锌或有毒涂料的金属笼
* **栖木**：提供不同直径的天然木栖木，帮助鸟类锻炼脚部
* **位置**：放在家中有活动的区域，但避免厨房（有害气体）和穿堂风

### 饮食设备

* 食物和水的容器应易于清洁且坚固
* 大型鹦鹉可能需要不锈钢碗来防止被啃咬

### 玩具和丰容物

* 提供各种安全的玩具供啃咬和玩耍
* 定期更换玩具以保持环境新鲜感
* 包括拼图玩具、响铃、梯子和秋千

## 营养需求

### 基础饮食

* **配方饲料**：高质量的专用鸟食作为基础饮食
* **新鲜水果和蔬菜**：占日常饮食的约25%
* **蛋白质来源**：少量熟蛋、豆类或适合的昆虫（根据鸟种）
* **零食**：适量的坚果或种子作为训练奖励

### 禁忌食物

* 巧克力、咖啡因、酒精
* 鳄梨、洋葱、大蒜
* 高盐或高糖食品
* 乳制品（大多数鸟类乳糖不耐受）

## 健康护理

### 日常观察

* 每天检查鸟的行为、食欲和排泄物
* 注意任何行为变化，鸟类通常会隐藏疾病症状

### 定期检查

* 至少每年一次兽医检查
* 找专门治疗鸟类的兽医

### 常见健康问题

* 呼吸道感染
* 羽毛啄食
* 营养缺乏
* 寄生虫感染

## 社交化和训练

* 每天提供至少1-2小时笼外时间（在安全环境中）
* 使用正面强化训练基本指令
* 定期与鸟类互动建立信任关系
* 尊重鸟类的自然行为和界限

## 环境安全

* 确保室内无有毒植物
* 避免使用不粘锅和自清洁烤箱（释放有毒气体）
* 关闭窗户和门防止逃脱
* 避免香水、蜡烛和空气清新剂

## 结论

宠物鸟可以成为令人愉快的伴侣，但需要专门的护理和关注。通过提供合适的环境、饮食和社交机会，您的羽毛朋友可以健康快乐地生活多年。`,
          user: '鸟类专家',
          userId: 'sample-user',
          time: currentTime,
          views: 76
        },
        {
          id: 'sample-4',
          name: '猫咪和狗狗的基础免疫接种指南',
          category: 'health',
          content: `# 猫咪和狗狗的基础免疫接种指南

宠物疫苗接种是预防多种严重甚至致命疾病的关键。本指南将帮助您了解宠物免疫的重要性以及建议的接种时间表。

## 为什么疫苗接种如此重要？

疫苗通过刺激宠物的免疫系统产生抗体来预防特定疾病。接种疫苗不仅保护您的宠物，还有助于防止疾病在宠物群体中传播，创建所谓的"群体免疫"。

## 犬类核心疫苗

### 什么是核心疫苗？

核心疫苗是指所有狗狗，无论其生活环境或生活方式如何，都应该接种的基本疫苗。

### 犬类核心疫苗包括：

1. **犬瘟热病毒 (CDV)**
   * 一种高度传染性的病毒性疾病，影响呼吸系统、消化系统和神经系统
   * 常通过空气传播或直接接触感染犬的分泌物传播
   * 症状包括发热、流鼻涕、咳嗽、呕吐、腹泻和癫痫发作
   * 死亡率高

2. **犬腺病毒 (CAV-1 和 CAV-2)**
   * 导致传染性肝炎，影响肝脏、肾脏、眼睛和呼吸系统
   * 通过接触感染犬的尿液、粪便或唾液传播
   * 症状包括发热、腹痛、呕吐和角膜混浊

3. **犬细小病毒 (CPV)**
   * 又称"犬瘟热"，是一种高度传染性的病毒，主要影响消化系统
   * 通过接触感染犬的粪便传播，病毒可在环境中存活数月
   * 症状包括严重呕吐、血性腹泻、脱水和体重减轻
   * 未经治疗的幼犬死亡率高达91%

4. **狂犬病**
   * 一种致命的病毒性疾病，影响中枢神经系统
   * 通过受感染动物的唾液传播，通常是通过咬伤
   * 一旦出现症状，几乎100%致命
   * 在大多数地区，狂犬病疫苗接种是法律要求

## 猫类核心疫苗

### 猫类核心疫苗包括：

1. **猫泛白细胞减少症病毒 (FPV)**
   * 也称为猫瘟热，是一种高度传染性的病毒
   * 攻击快速分裂的细胞，如骨髓、肠道和胎盘
   * 症状包括高烧、严重腹泻、脱水和突然死亡
   * 幼猫死亡率高达90%

2. **猫疱疹病毒 (FHV-1)/猫鼻支病毒**
   * 导致上呼吸道感染
   * 通过直接接触传播，病毒可长期潜伏
   * 症状包括打喷嚏、流鼻涕、结膜炎和角膜溃疡

3. **猫杯状病毒 (FCV)**
   * 导致口腔溃疡和上呼吸道症状
   * 高度传染性，通过直接接触或共享食物碗传播
   * 有些菌株可引起更严重的症状

4. **狂犬病**
   * 对室外猫尤为重要
   * 在许多地区是法律要求

## 推荐的疫苗接种时间表

### 幼犬疫苗时间表
* **6-8周龄**：第一次核心疫苗（犬瘟热、腺病毒、细小病毒）
* **10-12周龄**：核心疫苗重复接种
* **14-16周龄**：核心疫苗最后一次幼犬接种，狂犬病疫苗
* **1岁**：核心疫苗加强，狂犬病加强（如需要）
* **成年后**：根据兽医建议和当地要求，每1-3年接种一次

### 幼猫疫苗时间表
* **6-8周龄**：第一次核心疫苗（泛白细胞减少症、疱疹病毒、杯状病毒）
* **10-12周龄**：核心疫苗重复接种
* **14-16周龄**：核心疫苗最后一次幼猫接种，狂犬病疫苗
* **1岁**：核心疫苗加强，狂犬病加强（如需要）
* **成年后**：根据兽医建议和当地要求，每1-3年接种一次

## 非核心疫苗

根据您宠物的生活环境和风险因素，兽医可能会推荐额外的疫苗：

### 犬类非核心疫苗
* 犬副流感病毒
* 钩端螺旋体病
* 莱姆病
* 博德特氏菌

### 猫类非核心疫苗
* 猫白血病病毒 (FeLV)
* 猫艾滋病病毒 (FIV)
* 猫传染性腹膜炎 (FIP)
* 衣原体

## 注意事项和副作用

大多数宠物对疫苗的耐受性良好，但可能出现轻微的暂时性副作用：
* 注射部位轻微疼痛或肿胀
* 轻度发热
* 食欲下降
* 活动减少

如果您的宠物出现以下任何症状，请立即联系兽医：
* 面部肿胀
* 呕吐或腹泻
* 呼吸困难
* 虚弱或昏厥

## 结论

遵循兽医推荐的疫苗接种计划对保护您宠物的健康至关重要。每个宠物的需求都是独特的，因此与您的兽医讨论最适合您宠物的疫苗计划非常重要。`,
          user: '宠物健康专家',
          userId: 'sample-user',
          time: currentTime,
          views: 142
        },
        {
          id: 'sample-5',
          name: '保护濒危动物：我们能做什么',
          category: 'protection',
          content: `# 保护濒危动物：我们能做什么

## 全球濒危动物危机

据国际自然保护联盟(IUCN)红色名录统计，目前全球有超过37,400种物种面临灭绝威胁。这意味着：

* 哺乳动物中有26%濒临灭绝
* 两栖动物中有41%濒临灭绝
* 鸟类中有14%濒临灭绝
* 珊瑚礁中有33%濒临灭绝

## 主要威胁因素

### 栖息地丧失

这是濒危动物面临的最大威胁。由于以下原因，野生动物的自然家园正在消失：

* 森林砍伐
* 城市扩张
* 农业用地转换
* 采矿和资源开采
* 基础设施建设（道路、水坝等）

### 气候变化

全球气候变化正在以前所未有的速度改变生态系统：

* 极端天气事件增加
* 海平面上升淹没沿海栖息地
* 海洋酸化危及海洋生物
* 季节性变化打乱迁徙和繁殖周期

### 过度捕猎和捕捞

非法野生动物贸易和过度捕捞正在使许多物种数量急剧下降：

* 象牙、犀牛角和虎骨等用于传统医药
* 异国宠物贸易
* 肉食消费（包括鱼类的过度捕捞）
* 纪念品和奢侈品

### 污染

环境污染以多种方式影响野生动物：

* 塑料污染（特别是海洋生物）
* 农药和化肥
* 噪音和光污染
* 油污染

### 外来入侵物种

被引入到新生态系统的物种可能对当地野生动物造成毁灭性影响：

* 捕食当地物种
* 竞争资源
* 引入新疾病
* 改变栖息地

## 保护行动的重要性

保护濒危物种不仅仅是道德义务，还有多方面的重要性：

### 生态系统平衡

每个物种在生态系统中都扮演着独特的角色：

* 捕食者控制猎物种群
* 传粉者对植物繁殖至关重要
* 食草动物维持植被特性
* 分解者循环利用养分

当一个物种消失时，整个系统可能失去平衡。

### 生物多样性的价值

生物多样性为人类提供无数益处：

* 医药发现（许多药物源于自然化合物）
* 食物安全（野生品种提供遗传多样性）
* 生态系统服务（水净化、授粉、碳封存）
* 文化和精神价值

### 不可逆性

灭绝是永久性的—一旦一个物种消失，我们就永远失去了它：

* 数百万年的进化历程中断
* 独特的遗传信息丢失
* 未来发现的潜在价值永远消失

## 个人层面的行动

虽然濒危物种保护是一个全球性挑战，但个人行动确实能产生影响：

### 负责任的消费

* 避免购买由濒危物种制成的产品（象牙、犀牛角、虎皮等）
* 选择可持续捕捞的海鲜（使用海鲜指南应用）
* 购买经认证的可持续木材和纸制品（FSC认证）
* 减少棕榈油消费或选择可持续认证的棕榈油产品

### 减少碳足迹

* 使用公共交通、自行车或拼车
* 减少能源消耗
* 选择可再生能源
* 减少肉类消耗（特别是牛肉）

### 创造野生动物友好环境

* 在花园种植本地植物
* 避免使用农药和化学肥料
* 为本地野生动物创造栖息地（如鸟箱、蝙蝠箱）
* 减少光污染和噪音污染

### 宣传和教育

* 分享关于濒危物种的知识
* 支持野生动物保护法规
* 教育儿童了解野生动物保护的重要性
* 在社交媒体上提高认识

### 支持保护组织

* 向野生动物保护组织捐款
* 志愿参与保护项目
* "认养"濒危动物
* 参与公民科学项目

## 社区和全球层面的努力

更大规模的保护努力需要集体行动：

### 保护区和栖息地恢复

* 建立和维护保护区
* 恢复退化的栖息地
* 创建野生动物走廊连接分散的栖息地
* 支持原住民管理土地

### 反偷猎和执法

* 加强针对野生动物犯罪的法律
* 改善边境检查以打击非法野生动物贸易
* 支持反偷猎巡逻
* 为当地社区提供可持续生计的替代方案

### 繁殖和重新引入计划

* 保护濒危物种的遗传多样性
* 在人工环境中繁殖濒危物种
* 在合适的栖息地重新引入物种
* 种子银行和基因库保存

### 研究和监测

* 了解濒危物种的需求和威胁
* 监测种群以评估保护措施的效果
* 开发创新保护技术
* 分享知识和最佳实践

## 结论

保护濒危物种需要多层次的行动，从个人选择到全球合作。虽然挑战巨大，但世界各地的保护成功案例证明，通过共同努力，我们可以扭转趋势，确保地球的生物多样性为子孙后代所享有。

通过了解威胁、采取有意识的行动，并支持更广泛的保护努力，每个人都可以为保护我们星球上濒危的生命形式做出贡献。`,
          user: '动物保护专家',
          userId: 'sample-user',
          time: currentTime,
          views: 87
        }
      ]
      this.total = this.tableData.length
    },
    getCategoryName(category) {
      const categoryMap = {
        'health': '宠物健康',
        'training': '行为训练',
        'care': '饲养知识',
        'protection': '动物保护'
      }
      return categoryMap[category] || '未分类'
    },
    getPreviewContent(content) {
      // 移除 Markdown 标记，获取纯文本预览
      if (!content) return ''
      const plainText = content
        .replace(/#+\s+/g, '') // 移除标题标记
        .replace(/\*\*([^*]+)\*\*/g, '$1') // 移除粗体标记
        .replace(/\*([^*]+)\*/g, '$1') // 移除斜体标记
        .replace(/!\[.*?\]\(.*?\)/g, '[图片]') // 替换图片
        .replace(/\[([^\]]+)\]\(([^)]+)\)/g, '$1') // 替换链接
        .replace(/```[\s\S]*?```/g, '[代码块]') // 替换代码块
        .replace(/`([^`]+)`/g, '$1') // 移除行内代码标记
        .replace(/\n/g, ' ') // 替换换行为空格
      
      return plainText.length > 100 ? plainText.substring(0, 100) + '...' : plainText
    },
    save() {
      this.$refs.articleForm.validate((valid) => {
        if (valid) {
          this.request.post("/articlekp", this.form).then(res => {
            if (res.code === '200') {
              this.$message.success("发布成功")
              this.dialogFormVisible = false
              this.load()
            } else {
              this.$message.error("发布失败")
            }
          })
        } else {
          this.$message.error("请填写完整表单")
        }
      })
    },
    handleAdd() {
      this.dialogFormVisible = true
      this.form = {
        category: ""
      }
    },
    handleEdit(row) {
      this.form = JSON.parse(JSON.stringify(row))
      this.dialogFormVisible = true
      if (this.vis) {
        this.vis = false
      }
    },
    view(id, content) {
      this.content = content
      this.vis = true
      
      // 查找当前文章信息
      const article = this.tableData.find(item => item.id === id)
      if (article) {
        this.currentArticle = article
        
        // 增加阅读量
        this.request.put("/articlekp/view/" + id).then(() => {
          // 更新本地数据
          article.views = (article.views || 0) + 1
        })
      }
    },
    del(id) {
      this.$confirm('确定要删除这篇文章吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.request.delete("/articlekp/" + id).then(res => {
          if (res.code === '200') {
            this.$message.success("删除成功")
            if (this.vis) {
              this.vis = false
            }
            this.load()
          } else {
            this.$message.error("删除失败")
          }
        })
      }).catch(() => {})
    },
    handleCurrentChange(pageNum) {
      this.pageNum = pageNum
      this.load()
    },
    imgAdd(pos, $file) {
      // 第一步：将图片上传到服务器
      let formData = new FormData()
      formData.append('file', $file)
      this.request.post('/file/upload', formData).then(res => {
        // 第二步：将返回的图片地址替换到文本中
        this.$refs.md.$img2Url(pos, this.fixImageUrl(res))
      })
    }
  }
}
</script>

<style scoped>
.article-container {
  min-height: calc(100vh - 60px);
  padding: 20px;
  background-color: #f5f7fa;
  max-width: 1200px;
  margin: 0 auto;
}

.article-header {
  display: flex;
  align-items: center;
  margin-bottom: 30px;
  position: relative;
  padding-bottom: 15px;
}

.article-header i {
  font-size: 28px;
  margin-right: 10px;
  color: #409EFF;
}

.article-header span {
  font-size: 24px;
  font-weight: 600;
  color: #409EFF;
}

.header-divider {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100px;
  height: 3px;
  background: linear-gradient(to right, #409EFF, #67C23A);
  border-radius: 3px;
}

.article-content {
  margin-top: 20px;
  position: relative;
  min-height: 500px;
}

.loading-container {
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.empty-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 400px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.article-list {
  margin-top: 20px;
}

.article-card {
  margin-bottom: 30px;
  border-radius: 8px;
  overflow: hidden;
  transition: all 0.3s;
  height: 100%;
  cursor: pointer;
  border: none;
}

.article-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
}

.article-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.article-title {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
  margin: 10px 0;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  line-height: 1.5;
}

.article-category {
  font-size: 12px;
}

.article-views {
  font-size: 12px;
  color: #909399;
}

.article-card-content {
  margin-bottom: 15px;
}

.article-preview {
  color: #606266;
  font-size: 14px;
  line-height: 1.6;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  height: 4.8em;
}

.article-card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 12px;
  color: #909399;
  margin-top: 10px;
  border-top: 1px solid #f0f0f0;
  padding-top: 10px;
}

.article-info {
  display: flex;
  flex-direction: column;
}

.article-author, .article-time {
  margin-bottom: 5px;
}

.article-author i, .article-time i, .article-views i {
  margin-right: 5px;
}

.article-actions {
  display: flex;
  align-items: center;
}

.delete-btn {
  color: #F56C6C;
}

.pagination-container {
  display: flex;
  justify-content: center;
  margin-top: 40px;
  margin-bottom: 30px;
}

.dialog-footer {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}

.floating-btn {
  position: fixed;
  bottom: 30px;
  right: 30px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  z-index: 10;
  width: 60px;
  height: 60px;
}

.article-view-dialog >>> .el-dialog {
  margin: 30px auto !important;
  display: flex;
  flex-direction: column;
  max-height: 80vh;
  border-radius: 8px;
}

.article-view-dialog >>> .el-dialog__header {
  padding: 15px 20px;
  border-bottom: 1px solid #ebeef5;
}

.article-view-dialog >>> .el-dialog__body {
  flex: 1;
  overflow-y: auto;
  padding: 0;
}

.article-view-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  border-bottom: 1px solid #ebeef5;
  background-color: #f9f9f9;
}

.article-view-info {
  display: flex;
  align-items: center;
  font-size: 14px;
  color: #909399;
  flex-wrap: wrap;
  gap: 15px;
}

.article-view-author, .article-view-time, .article-view-views {
  margin-right: 15px;
}

.article-view-author i, .article-view-time i, .article-view-views i {
  margin-right: 5px;
}

.article-view-content {
  padding: 20px;
  max-width: 1000px;
  margin: 0 auto;
}

/* 覆盖 mavon-editor 样式 */
.md {
  min-height: 500px;
  z-index: 1;
}

@media (max-width: 768px) {
  .article-view-header {
    flex-direction: column;
    align-items: flex-start;
  }
  
  .article-view-actions {
    margin-top: 10px;
  }
  
  .article-view-info {
    margin-bottom: 10px;
  }
}
</style>
