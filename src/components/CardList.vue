<template>
  <div class="card-list">
    <div 
      v-for="(card, index) in cards" 
      :key="index"
      class="card"
      :class="[currentStyle, { 'cover-card': card.isCover }]"
      :ref="el => { if (el) cardRefs[index] = el }"
    >
      <div class="card-content" :class="{ 'cover-card': card.isCover }">
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
          @click="$emit('delete-card', index)"
        >
          <i class="fas fa-trash-alt"></i>
        </div>
      </div>

      <!-- 下载按钮 -->
      <div 
        class="download-btn"
        @click.prevent.stop="downloadCard(index)"
      >
        <i class="fas fa-download"></i>
      </div>
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas'
import { ref, onBeforeUpdate } from 'vue'

export default {
  name: 'CardList',
  props: {
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
  },
  setup() {
    const cardRefs = ref([])

    // 在更新前清空 refs 数组
    onBeforeUpdate(() => {
      cardRefs.value = []
    })

    return {
      cardRefs
    }
  },
  methods: {
    async downloadCard(index) {
      console.log('开始下载卡片:', index)
      try {
        const card = this.cardRefs[index]
        console.log('找到卡片元素:', card)
        
        if (!card) {
          console.error('找不到卡片元素')
          return
        }

        // 临时隐藏按钮
        const buttons = card.querySelectorAll('.download-btn, .delete-btn, .card-actions')
        buttons.forEach(btn => btn.style.display = 'none')

        try {
          // 生成图片
          const canvas = await html2canvas(card, {
            scale: 4,
            backgroundColor: null,
            useCORS: true,
            allowTaint: true,
            logging: true,
            onclone: function(clonedDoc) {
              // 在克隆的文档中也隐藏按钮
              const clonedButtons = clonedDoc.querySelectorAll('.download-btn, .delete-btn, .card-actions')
              clonedButtons.forEach(btn => btn.style.display = 'none')
            }
          })

          // 恢复按钮显示
          buttons.forEach(btn => btn.style.display = '')

          // 创建下载链接
          const link = document.createElement('a')
          link.download = `小红书卡片-${Date.now()}.png`
          link.href = canvas.toDataURL('image/png')
          document.body.appendChild(link)
          link.click()
          document.body.removeChild(link)
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
  },
  computed: {
    getStyleIcon() {
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
      return icons[this.currentStyle] || 'fa-file-alt'
    },
    getIconGradient() {
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
      return { background: gradients[this.currentStyle] || gradients['default'] }
    }
  }
}
</script>

<style scoped>
.card-list {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
}

.card-actions {
  position: absolute;
  top: 10px;
  right: 10px;
  display: flex;
  gap: 10px;
  opacity: 0;
  transition: opacity 0.3s;
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

.delete-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 4px 12px rgba(255, 107, 107, 0.3);
  background: rgba(255, 107, 107, 1);
}

.download-btn:active,
.delete-btn:active {
  transform: scale(0.95);
}
</style> 