<template>
  <div class="card-list">
    <div 
      v-for="(card, index) in cards" 
      :key="index"
      class="card"
      :class="[currentStyle, { 'cover-card': card.isCover }]"
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
          class="download-btn"
          @click="$emit('download-card', card)"
        >
          <i class="fas fa-download"></i>
        </div>
        <div 
          class="delete-btn"
          @click="$emit('delete-card', index)"
        >
          <i class="fas fa-trash-alt"></i>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
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

.download-btn,
.delete-btn {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  cursor: pointer;
  background: rgba(255, 255, 255, 0.9);
  transition: all 0.3s;
}

.delete-btn {
  background: rgba(255, 107, 107, 0.8);
  color: white;
}

.download-btn:hover,
.delete-btn:hover {
  transform: scale(1.1);
}
</style> 