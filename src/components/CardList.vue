<template>
  <div class="card-list">
    <div 
      v-for="(card, index) in cards" 
      :key="index"
      class="card"
      :class="[
        currentStyle,
        { 'cover-card': card.isCover },
        card.isCover && card.bgType === 'style' ? `cover-style-${card.coverStyle}` : ''
      ]"
      :ref="el => { if (el) cardRefs[index] = el }"
    >
      <div 
        class="card-content" 
        :class="[
          { 'cover-card': card.isCover },
          card.isCover && card.bgType === 'style' ? `cover-style-${card.coverStyle}` : ''
        ]"
        :style="getCardStyle(card)"
      >
        <!-- 普通卡片内容 -->
        <template v-if="!card.isCover">
          <div class="card-header">
            <div class="icon-box" :style="getIconGradient">
              <i class="icon fas" :class="getStyleIcon"></i>
            </div>
            <h2 
              :style="{
                fontSize: `${currentFontSize * 1.2}px`,
                textAlign: currentTextAlign
              }"
            >
              {{ card.title }}
            </h2>
          </div>
          <div class="divider"></div>
          <div class="content-item">
            <div 
              class="text-content"
              :style="{
                fontSize: `${currentFontSize}px`,
                textAlign: currentTextAlign
              }"
              v-html="card.content"
            ></div>
          </div>
        </template>

        <!-- 封面卡片内容 -->
        <template v-else>
          <i 
            v-if="card.icon && card.icon !== 'fa-none'"
            :class="['fas', card.icon, 'cover-icon']"
            :style="{
              fontSize: card.iconSize,
              color: card.iconColor
            }"
          ></i>
          <div 
            class="cover-title"
            :style="{
              fontFamily: card.titleFont,
              fontSize: card.titleSize,
              fontWeight: card.titleWeight,
              textAlign: card.titleAlign,
              color: card.titleColor
            }"
          >
            {{ card.title }}
          </div>
          <div 
            class="cover-subtitle"
            :style="{
              fontFamily: card.subtitleFont,
              fontSize: card.subtitleSize,
              fontWeight: card.subtitleWeight,
              textAlign: card.subtitleAlign,
              color: card.subtitleColor
            }"
          >
            {{ card.subtitle }}
          </div>
          <div 
            v-if="card.quote"
            class="cover-quote"
            :style="{
              fontFamily: card.quoteFont,
              fontSize: card.quoteSize,
              fontWeight: card.quoteWeight,
              color: card.quoteColor
            }"
          >
            {{ card.quote }}
          </div>
          <div 
            v-if="card.showDate"
            class="cover-date"
            :style="{
              fontFamily: card.dateFont,
              fontSize: card.dateSize,
              fontWeight: card.dateWeight,
              color: card.dateColor
            }"
          >
            {{ card.date }}
          </div>
          <div 
            v-if="card.showAuthor"
            class="cover-author"
            :style="{
              fontFamily: card.authorFont,
              fontSize: card.authorSize,
              fontWeight: card.authorWeight,
              color: card.authorColor
            }"
          >
            {{ card.author }}
          </div>
        </template>
      </div>

      <!-- 卡片操作按钮 -->
      <div class="card-actions">
        <div 
          class="delete-btn"
          @click="emit('delete-card', index)"
        >
          <i class="fas fa-trash-alt"></i>
        </div>
      </div>

      <!-- 下载按钮 -->
      <div 
        class="download-btn"
        @click.stop="downloadCard(index)"
      >
        <i class="fas fa-download"></i>
      </div>
    </div>
  </div>
</template>

<script setup>
import html2canvas from 'html2canvas'
import { ref, onBeforeUpdate, computed } from 'vue'

// Props 定义
const props = defineProps({
  cards: {
    type: Array,
    required: true
  },
  currentStyle: {
    type: String,
    default: 'default'
  },
  currentTextAlign: {
    type: String,
    default: 'left'
  },
  currentFontSize: {
    type: Number,
    default: 16
  }
})

// Emits 定义
const emit = defineEmits(['delete-card'])

// 卡片引用数组
const cardRefs = ref([])

// 在更新前清空 refs 数组
onBeforeUpdate(() => {
  cardRefs.value = []
})

// 下载卡片方法
const downloadCard = async (index) => {
  console.log('点击了下载按钮')
  console.log('开始下载卡片:', index)
  console.log('cardRefs:', cardRefs.value)
  try {
    const card = cardRefs.value[index]
    if (!card) {
      console.error('找不到卡片元素，index:', index)
      return
    }
    console.log('找到卡片元素:', card)

    // 临时隐藏按钮
    const buttons = card.querySelectorAll('.download-btn, .delete-btn, .card-actions')
    console.log('找到要隐藏的按钮:', buttons.length, '个')
    buttons.forEach(btn => btn.style.display = 'none')

    try {
      console.log('开始生成图片...')
      // 生成图片
      const canvas = await html2canvas(card, {
        scale: 4,
        backgroundColor: null,
        useCORS: true,
        allowTaint: true,
        logging: true,
        onclone: function(clonedDoc) {
          console.log('克隆文档成功')
          // 在克隆的文档中也隐藏按钮
          const clonedButtons = clonedDoc.querySelectorAll('.download-btn, .delete-btn, .card-actions')
          clonedButtons.forEach(btn => btn.style.display = 'none')
        }
      })

      console.log('图片生成成功，准备下载')
      // 创建下载链接
      const link = document.createElement('a')
      link.download = `小红书卡片-${Date.now()}.png`
      link.href = canvas.toDataURL('image/png')
      document.body.appendChild(link)
      link.click()
      document.body.removeChild(link)
      console.log('下载完成')
    } catch (error) {
      console.error('生成图片失败:', error)
      throw error
    } finally {
      // 确保按钮恢复显示
      buttons.forEach(btn => btn.style.display = '')
    }
  } catch (error) {
    console.error('下载卡片失败:', error)
    alert('下载失败，请重试')
  }
}

// 计算属性
const getStyleIcon = computed(() => {
  const icons = {
    'default': 'fa-quote-right',
    'cyberpunk': 'fa-microchip',
    'neon': 'fa-bolt',
    'glow': 'fa-star',
    'gradient': 'fa-palette',
    'dashed': 'fa-fire',
    'cosmos': 'fa-space-shuttle',
    'aurora': 'fa-moon',
    'twilight': 'fa-cloud-moon',
    'ocean': 'fa-water',
    'forest': 'fa-tree',
    'sunset': 'fa-sun'
  }
  return icons[props.currentStyle] || 'fa-file-alt'
})

const getIconGradient = computed(() => {
  const gradients = {
    'default': 'linear-gradient(45deg, #6366f1, #8b5cf6)',
    'cyberpunk': 'linear-gradient(45deg, #ec4899, #6366f1)',
    'neon': 'linear-gradient(135deg, #00f3ff, #ff00ff)',
    'glow': 'linear-gradient(45deg, #ff6b6b, #4ecdc4)',
    'gradient': 'linear-gradient(45deg, #6366f1, #ec4899)',
    'dashed': 'linear-gradient(45deg, #ffd700, #ff9100)',
    'aurora': 'linear-gradient(135deg, var(--aurora-green), var(--aurora-blue))',
    'twilight': 'linear-gradient(135deg, var(--twilight-purple), var(--twilight-pink))',
    'ocean': 'linear-gradient(135deg, var(--ocean-blue), var(--ocean-teal))',
    'forest': 'linear-gradient(135deg, var(--forest-green), var(--forest-teal))',
    'sunset': 'linear-gradient(135deg, var(--sunset-orange), var(--sunset-pink))',
    'cosmos': 'linear-gradient(135deg, var(--cosmos-blue), var(--cosmos-purple))'
  }
  return { background: gradients[props.currentStyle] || gradients['default'] }
})

// 添加获取卡片样式的方法
const getCardStyle = (card) => {
  if (!card.isCover || card.bgType !== 'color') {
    return {}
  }

  if (card.bgStyle === 'solid') {
    return {
      background: `rgba(${hexToRgb(card.bgColor)}, ${card.bgOpacity})`
    }
  } else {
    if (card.gradientDirection === 'circle') {
      return {
        background: `radial-gradient(circle, ${card.gradientColor1}, ${card.gradientColor2})`
      }
    } else {
      return {
        background: `linear-gradient(${card.gradientDirection}, ${card.gradientColor1}, ${card.gradientColor2})`
      }
    }
  }
}

// 添加 hexToRgb 辅助函数
const hexToRgb = (hex) => {
  const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex)
  return result
    ? `${parseInt(result[1], 16)}, ${parseInt(result[2], 16)}, ${parseInt(result[3], 16)}`
    : null
}
</script>

<style scoped>
.card-list {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
  width: 100%;
  padding: 20px;
  box-sizing: border-box;
}

/* 添加卡片基础样式 */
.card {
  width: 280px !important;
  min-width: 280px;
  max-width: 280px;
  height: 373px;
  flex: 0 0 280px;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 25px;
  padding: 3px;
  position: relative;
  overflow: hidden;
  transition: all 0.3s;
  background-clip: padding-box;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  border: 3px solid transparent;
  box-sizing: border-box;
}

.card-content {
  position: relative;
  width: 100%;
  height: 367px;
  border-radius: 22px;
  padding: 20px;
  font-size: 16px;
  background: rgba(255,255,255,0.95);
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
}

/* 封面卡片基础样式 */
.cover-card {
  width: 100% !important;
  height: 100%;
  position: relative;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 30px;
  box-sizing: border-box;
}

.cover-title {
  font-size: 2.5em;
  font-weight: 700;
  margin-bottom: 15px;
  text-align: center;
  color: white;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
  font-family: 'Noto Serif SC', serif;
  max-width: 90%;
  line-height: 1.3;
  position: relative;
  z-index: 1;
}

.cover-subtitle {
  font-size: 1.2em;
  font-weight: 400;
  margin-bottom: 20px;
  text-align: center;
  color: rgba(255, 255, 255, 0.9);
  max-width: 80%;
  line-height: 1.5;
  font-family: 'Noto Sans SC', sans-serif;
  position: relative;
  z-index: 1;
}

.cover-icon {
  font-size: 3em;
  margin-bottom: 20px;
  color: white;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
  position: relative;
  z-index: 1;
}

.cover-date {
  position: absolute;
  top: 20px;
  right: 20px;
  font-family: 'Noto Sans SC', sans-serif;
  font-size: 0.8em;
  color: #fff;
  z-index: 1;
}

.cover-author {
  position: absolute;
  bottom: 20px;
  right: 20px;
  font-family: 'Noto Sans SC', sans-serif;
  font-size: 0.8em;
  color: #fff;
  z-index: 1;
}

/* 操作按钮样式 */
.card-actions {
  position: absolute;
  top: 10px;
  right: 10px;
  display: flex;
  gap: 10px;
  opacity: 0;
  transition: opacity 0.3s;
  z-index: 2;
}

.card:hover .card-actions {
  opacity: 1;
}

.delete-btn {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  cursor: pointer;
  background: rgba(255, 107, 107, 0.8);
  color: white;
  transition: all 0.3s;
}

.download-btn {
  position: absolute;
  bottom: 10px;
  right: 10px;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  cursor: pointer;
  background: rgba(255, 255, 255, 0.95);
  color: #1a73e8;
  transition: all 0.3s;
  opacity: 0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  z-index: 3;
}

.card:hover .download-btn {
  opacity: 1;
}

.download-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 4px 12px rgba(26, 115, 232, 0.4);
  background: #1a73e8;
  color: white;
}

.download-btn:active {
  transform: scale(0.95);
}

.delete-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 4px 12px rgba(255, 107, 107, 0.3);
  background: rgba(255, 107, 107, 1);
}

.delete-btn:active {
  transform: scale(0.95);
}

/* 封面样式 */
/* 1. 极简主义风格 */
.cover-style-11 {
  background: #ffffff !important;
  position: relative;
}

.cover-style-11 .cover-title,
.cover-style-11 .cover-subtitle,
.cover-style-11 .cover-icon {
  color: #000000;
  text-shadow: none;
}

.cover-style-11::before {
  content: '';
  position: absolute;
  top: 20px;
  left: 20px;
  right: 20px;
  height: 1px;
  background: #000;
  opacity: 0.2;
}

.cover-style-11::after {
  content: '';
  position: absolute;
  bottom: 20px;
  left: 20px;
  right: 20px;
  height: 1px;
  background: #000;
  opacity: 0.2;
}

/* 2. 大胆现代风格 */
.cover-style-12 {
  background: #0f0f0f !important;
  position: relative;
  overflow: hidden;
}

.cover-style-12::before {
  content: '';
  position: absolute;
  width: 150px;
  height: 150px;
  background: #ff2d55;
  top: -30px;
  right: -30px;
  border-radius: 50%;
  opacity: 0.8;
  z-index: 0;
}

.cover-style-12::after {
  content: '';
  position: absolute;
  width: 100px;
  height: 100px;
  background: #00f2fe;
  bottom: -20px;
  left: -20px;
  border-radius: 50%;
  opacity: 0.8;
  z-index: 0;
}

/* 3. 优雅复古风格 */
.cover-style-13 {
  background: #f8f3e9 !important;
  position: relative;
  overflow: hidden;
}

.cover-style-13 .cover-title,
.cover-style-13 .cover-subtitle,
.cover-style-13 .cover-icon {
  color: #5c3d2e;
  text-shadow: none;
}

.cover-style-13::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><rect width="100" height="100" fill="none" stroke="%23a67c52" stroke-width="0.5" stroke-opacity="0.2"/></svg>');
  opacity: 0.3;
}

/* 太空金属风 */
.cover-style-18 {
  background: #36454F !important;
  position: relative;
  overflow: hidden;
}

.cover-style-18::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: radial-gradient(circle at 70% 20%, #FFD700 0%, transparent 25%);
  opacity: 0.6;
}

/* 学术蓝 - 考研主题 */
.cover-style-19 {
  background: linear-gradient(135deg, #e6f7ff, #bae7ff, #91d5ff) !important;
  position: relative;
  overflow: hidden;
}

.cover-style-19 .cover-title,
.cover-style-19 .cover-subtitle,
.cover-style-19 .cover-icon {
  color: #0050b3;
  text-shadow: none;
}

.cover-style-19::before {
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20"><rect width="20" height="20" fill="none"/><path d="M10,2 L18,8 L14,8 L14,16 L6,16 L6,8 L2,8 L10,2 Z" fill="%231890ff" fill-opacity="0.1"/></svg>');
  opacity: 0.3;
}

/* 活力橙 - 教育主题 */
.cover-style-20 {
  background: #fff9e6 !important;
  position: relative;
  overflow: hidden;
}

.cover-style-20 .cover-title,
.cover-style-20 .cover-subtitle,
.cover-style-20 .cover-icon {
  color: #fa8c16;
  text-shadow: none;
}

.cover-style-20::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 8px;
  background: linear-gradient(90deg, #ffd666, #ffa940, #fa8c16);
}

.cover-style-20::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 8px;
  background: linear-gradient(90deg, #ffd666, #ffa940, #fa8c16);
}

/* 清新绿 - 学习主题 */
.cover-style-21 {
  background: #f6ffed !important;
  position: relative;
  overflow: hidden;
}

.cover-style-21 .cover-title,
.cover-style-21 .cover-subtitle,
.cover-style-21 .cover-icon {
  color: #389e0d;
  text-shadow: none;
}

.cover-style-21::before {
  content: '';
  position: absolute;
  top: -50px;
  right: -50px;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  background: rgba(82, 196, 26, 0.2);
}

.cover-style-21::after {
  content: '';
  position: absolute;
  bottom: -30px;
  left: -30px;
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: rgba(82, 196, 26, 0.15);
}

/* 方格纸 - 学习笔记主题 */
.cover-style-22 {
  background-color: #fffbe6 !important;
  background-image: linear-gradient(#fadb14 1px, transparent 1px),
                    linear-gradient(90deg, #fadb14 1px, transparent 1px);
  background-size: 20px 20px;
  background-position: -1px -1px;
  position: relative;
}

.cover-style-22 .cover-title,
.cover-style-22 .cover-subtitle,
.cover-style-22 .cover-icon {
  color: #ad8b00;
  text-shadow: none;
}

.cover-style-22::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(255, 255, 255, 0.7);
  z-index: 0;
}

/* 考研金 - 考研主题 */
.cover-style-23 {
  background: #000000 !important;
  position: relative;
  overflow: hidden;
}

.cover-style-23 .cover-title {
  color: #faad14;
}

.cover-style-23::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #faad14;
  opacity: 0.2;
  z-index: 0;
}

/* 继续添加其他样式... */
</style> 