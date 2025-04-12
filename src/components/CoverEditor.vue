<template>
  <div class="cover-editor-overlay" @click="$emit('close')">
    <div class="cover-editor-container" @click.stop>
      <div class="cover-editor-header">
        <h2 class="cover-editor-title">封面编辑</h2>
        <button class="cover-editor-close" @click="$emit('close')">
          <i class="fas fa-times"></i>
        </button>
      </div>

      <div class="editor-content">
        <div class="editor-left">
          <!-- 背景设置 -->
          <div class="cover-editor-section">
            <div class="cover-editor-section-title">封面背景设置</div>
            <div class="cover-bg-type-selection">
              <div class="cover-bg-option">
                <input 
                  type="radio" 
                  id="cover-bg-color-option" 
                  v-model="bgType"
                  value="color"
                >
                <label for="cover-bg-color-option">使用背景颜色</label>
              </div>
              <div class="cover-bg-option">
                <input 
                  type="radio" 
                  id="cover-bg-style-option" 
                  v-model="bgType"
                  value="style"
                >
                <label for="cover-bg-style-option">使用封面样式</label>
              </div>
            </div>
          </div>

          <!-- 样式选择器 -->
          <div 
            v-show="bgType === 'style'"
            class="cover-editor-section"
          >
            <div class="cover-editor-section-title">选择封面样式</div>
            <div class="cover-styles-container">
              <div 
                v-for="style in coverStyles"
                :key="style.id"
                class="cover-style-item"
                :class="[
                  `cover-style-${style.id}`,
                  { active: currentCoverStyle === style.id }
                ]"
                @click="selectCoverStyle(style.id)"
              >
                <span>{{ style.name }}</span>
              </div>
            </div>
          </div>

          <!-- 标题设置 -->
          <div class="cover-editor-section">
            <div class="cover-editor-section-title">封面标题</div>
            <input 
              type="text"
              class="cover-input"
              v-model="coverData.title"
              placeholder="输入封面标题..."
            >
            <div class="cover-font-controls-wrapper">
              <div class="cover-font-controls">
                <select v-model="coverData.titleFont" class="cover-font-family-select">
                  <option v-for="font in fonts" :key="font.value" :value="font.value">
                    {{ font.label }}
                  </option>
                </select>
                <select v-model="coverData.titleSize" class="cover-font-size-select">
                  <option value="2em">小</option>
                  <option value="2.5em">中</option>
                  <option value="3em">大</option>
                  <option value="3.5em">特大</option>
                </select>
                <select v-model="coverData.titleWeight" class="cover-font-weight-select">
                  <option value="400">细</option>
                  <option value="500">中等</option>
                  <option value="700">粗体</option>
                </select>
                <select v-model="coverData.titleAlign" class="cover-text-align-select">
                  <option value="left">左对齐</option>
                  <option value="center">居中</option>
                  <option value="right">右对齐</option>
                </select>
                <input 
                  type="color"
                  class="cover-color-picker"
                  v-model="coverData.titleColor"
                >
              </div>
            </div>
          </div>

          <!-- 副标题设置 -->
          <div class="cover-editor-section">
            <div class="cover-editor-section-title">副标题</div>
            <input 
              type="text"
              class="cover-input"
              v-model="coverData.subtitle"
              placeholder="输入副标题..."
            >
            <div class="cover-font-controls-wrapper">
              <div class="cover-font-controls">
                <select v-model="coverData.subtitleFont" class="cover-font-family-select">
                  <option v-for="font in fonts" :key="font.value" :value="font.value">
                    {{ font.label }}
                  </option>
                </select>
                <select v-model="coverData.subtitleSize" class="cover-font-size-select">
                  <option value="1em">小</option>
                  <option value="1.2em">中</option>
                  <option value="1.5em">大</option>
                </select>
                <select v-model="coverData.subtitleWeight" class="cover-font-weight-select">
                  <option value="300">细</option>
                  <option value="400">常规</option>
                  <option value="500">中等</option>
                  <option value="700">粗体</option>
                </select>
                <select v-model="coverData.subtitleAlign" class="cover-text-align-select">
                  <option value="left">左对齐</option>
                  <option value="center">居中</option>
                  <option value="right">右对齐</option>
                </select>
                <input 
                  type="color"
                  class="cover-color-picker"
                  v-model="coverData.subtitleColor"
                >
              </div>
            </div>
          </div>

          <!-- 图标设置 -->
          <div class="cover-editor-section">
            <div class="cover-editor-section-title">图标</div>
            <div class="cover-font-controls">
              <select v-model="coverData.icon" class="cover-icon-select">
                <option v-for="icon in icons" :key="icon.value" :value="icon.value">
                  {{ icon.label }}
                </option>
              </select>
              <select v-model="coverData.iconSize" class="cover-font-size-select">
                <option value="2em">小</option>
                <option value="3em">中</option>
                <option value="4em">大</option>
              </select>
              <input 
                type="color"
                class="cover-color-picker"
                v-model="coverData.iconColor"
              >
            </div>
          </div>

          <!-- 其他设置（日期、作者等） -->
          <div class="cover-editor-section">
            <div class="cover-editor-section-title">其他信息</div>
            <!-- 日期设置 -->
            <div class="cover-date-controls">
              <input 
                type="checkbox"
                id="cover-show-date"
                v-model="coverData.showDate"
              >
              <label for="cover-show-date">显示日期</label>
              <input 
                v-if="coverData.showDate"
                type="text"
                class="cover-input"
                v-model="coverData.date"
                placeholder="输入日期..."
              >
            </div>
            <!-- 作者设置 -->
            <div class="cover-author-controls">
              <input 
                type="checkbox"
                id="cover-show-author"
                v-model="coverData.showAuthor"
              >
              <label for="cover-show-author">显示作者</label>
              <input 
                v-if="coverData.showAuthor"
                type="text"
                class="cover-input"
                v-model="coverData.author"
                placeholder="输入作者名称..."
              >
            </div>
          </div>

          <!-- 背景颜色设置 -->
          <div 
            v-show="bgType === 'color'"
            class="cover-editor-section"
          >
            <div class="cover-editor-section-title">背景颜色</div>
            <div class="cover-bg-controls">
              <div class="cover-bg-type">
                <input 
                  type="radio"
                  id="cover-bg-solid"
                  v-model="coverData.bgStyle"
                  value="solid"
                >
                <label for="cover-bg-solid">纯色背景</label>
                <input 
                  type="radio"
                  id="cover-bg-gradient"
                  v-model="coverData.bgStyle"
                  value="gradient"
                >
                <label for="cover-bg-gradient">渐变背景</label>
              </div>

              <div v-if="coverData.bgStyle === 'solid'">
                <input 
                  type="color"
                  class="cover-color-picker"
                  v-model="coverData.bgColor"
                >
                <input 
                  type="range"
                  v-model="coverData.bgOpacity"
                  min="0"
                  max="1"
                  step="0.1"
                >
                <span>不透明度: {{ Math.round(coverData.bgOpacity * 100) }}%</span>
              </div>

              <div v-else>
                <div class="gradient-colors">
                  <input 
                    type="color"
                    class="cover-color-picker"
                    v-model="coverData.gradientColor1"
                  >
                  <input 
                    type="color"
                    class="cover-color-picker"
                    v-model="coverData.gradientColor2"
                  >
                </div>
                <select v-model="coverData.gradientDirection">
                  <option value="to right">从左到右</option>
                  <option value="to bottom">从上到下</option>
                  <option value="to bottom right">左上到右下</option>
                  <option value="to bottom left">右上到左下</option>
                  <option value="circle">径向渐变</option>
                </select>
              </div>
            </div>
          </div>
        </div>

        <div class="editor-right">
          <!-- 预览区域 -->
          <div class="cover-editor-section">
            <div class="cover-preview">
              <div 
                class="cover-card"
                :class="previewClasses"
                :style="previewStyles"
              >
                <i 
                  v-if="coverData.icon !== 'fa-none'"
                  :class="['fas', coverData.icon, 'cover-icon']"
                  :style="{
                    fontSize: coverData.iconSize,
                    color: coverData.iconColor
                  }"
                ></i>
                <div 
                  class="cover-title"
                  :style="{
                    fontFamily: coverData.titleFont,
                    fontSize: coverData.titleSize,
                    fontWeight: coverData.titleWeight,
                    textAlign: coverData.titleAlign,
                    color: coverData.titleColor
                  }"
                >
                  {{ coverData.title || '标题' }}
                </div>
                <div 
                  class="cover-subtitle"
                  :style="{
                    fontFamily: coverData.subtitleFont,
                    fontSize: coverData.subtitleSize,
                    fontWeight: coverData.subtitleWeight,
                    textAlign: coverData.subtitleAlign,
                    color: coverData.subtitleColor
                  }"
                >
                  {{ coverData.subtitle || '副标题' }}
                </div>
                <div 
                  v-if="coverData.showDate"
                  class="cover-date"
                >
                  {{ coverData.date }}
                </div>
                <div 
                  v-if="coverData.showAuthor"
                  class="cover-author"
                >
                  {{ coverData.author }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="cover-actions">
        <button class="cover-action-btn" @click="$emit('close')">取消</button>
        <button class="cover-action-btn primary" @click="saveCover">
          <i class="fas fa-save"></i> 保存封面
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, reactive, computed } from 'vue'

export default {
  name: 'CoverEditor',
  props: {
    currentStyle: {
      type: String,
      default: 'default'
    }
  },
  setup(props, { emit }) {
    const bgType = ref('style')
    const currentCoverStyle = ref('11')

    const coverData = reactive({
      title: '仟流Card',
      subtitle: '一键生成精美小红书风格卡片',
      titleFont: "'Noto Serif SC', serif",
      titleSize: '2.5em',
      titleWeight: '700',
      titleAlign: 'center',
      titleColor: '#ffffff',
      subtitleFont: "'Noto Sans SC', sans-serif",
      subtitleSize: '1.2em',
      subtitleWeight: '400',
      subtitleAlign: 'center',
      subtitleColor: '#ffffff',
      icon: 'fa-none',
      iconSize: '3em',
      iconColor: '#ffffff',
      showDate: false,
      date: '',
      showAuthor: false,
      author: '',
      bgStyle: 'solid',
      bgColor: '#1e293b',
      bgOpacity: 1,
      gradientColor1: '#1e293b',
      gradientColor2: '#4a5568',
      gradientDirection: 'to right'
    })

    const fonts = [
      { label: '宋体', value: "'Noto Serif SC', serif" },
      { label: '黑体', value: "'Noto Sans SC', sans-serif" },
      { label: '思源宋体', value: "'Source Han Serif CN VF', serif" },
      { label: '汇文明朝体', value: "'Huiwen-mincho', serif" },
      { label: '抖音美好体', value: "'Douyin Sans', sans-serif" },
      { label: '程荣光刻楷', value: "'程荣光刻楷', serif" }
    ]

    const icons = [
      { label: '无图标', value: 'fa-none' },
      { label: '星星', value: 'fa-star' },
      { label: '爱心', value: 'fa-heart' },
      { label: '火焰', value: 'fa-fire' },
      // ... 其他图标
    ]

    const coverStyles = [
      { id: '11', name: '极简' },
      { id: '12', name: '现代' },
      { id: '13', name: '复古' },
      // ... 其他样式
    ]

    const previewClasses = computed(() => {
      const classes = [props.currentStyle]
      if (bgType.value === 'style') {
        classes.push(`cover-style-${currentCoverStyle.value}`)
      }
      return classes
    })

    const previewStyles = computed(() => {
      if (bgType.value === 'color') {
        if (coverData.bgStyle === 'solid') {
          return {
            background: `rgba(${hexToRgb(coverData.bgColor)}, ${coverData.bgOpacity})`
          }
        } else {
          if (coverData.gradientDirection === 'circle') {
            return {
              background: `radial-gradient(circle, ${coverData.gradientColor1}, ${coverData.gradientColor2})`
            }
          } else {
            return {
              background: `linear-gradient(${coverData.gradientDirection}, ${coverData.gradientColor1}, ${coverData.gradientColor2})`
            }
          }
        }
      }
      return {}
    })

    const selectCoverStyle = (styleId) => {
      currentCoverStyle.value = styleId
    }

    const hexToRgb = (hex) => {
      const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex)
      return result
        ? `${parseInt(result[1], 16)}, ${parseInt(result[2], 16)}, ${parseInt(result[3], 16)}`
        : null
    }

    const saveCover = () => {
      emit('save', {
        ...coverData,
        isCover: true,
        coverStyle: currentCoverStyle.value,
        bgType: bgType.value
      })
    }

    return {
      bgType,
      currentCoverStyle,
      coverData,
      fonts,
      icons,
      coverStyles,
      previewClasses,
      previewStyles,
      selectCoverStyle,
      saveCover
    }
  }
}
</script>

<style scoped>
.cover-editor-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  z-index: 999;
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
}

.cover-editor-container {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 90%;
  max-width: 1200px;
  max-height: 90vh;
  background: rgba(30, 41, 59, 0.95);
  border-radius: 20px;
  padding: 25px;
  box-shadow: 0 0 50px rgba(0, 0, 0, 0.5);
  z-index: 1000;
  overflow-y: auto;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

.editor-content {
  display: flex;
  gap: 30px;
}

.editor-left {
  flex: 1;
  max-width: 600px;
}

.editor-right {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

/* 其他样式... */
</style> 