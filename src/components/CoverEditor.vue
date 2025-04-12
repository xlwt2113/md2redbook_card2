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
            <div class="cover-font-controls-wrapper">
              <input 
                type="text"
                class="cover-input"
                v-model="coverData.title"
                placeholder="请输入标题..."
              >
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
            <div class="cover-font-controls-wrapper">
              <input 
                type="text"
                class="cover-input"
                v-model="coverData.subtitle"
                placeholder="请输入副标题..."
              >
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
      { label: '闪电', value: 'fa-bolt' },
      { label: '皇冠', value: 'fa-crown' },
      { label: '宝石', value: 'fa-gem' },
      { label: '魔法', value: 'fa-magic' },
      { label: '太阳', value: 'fa-sun' },
      { label: '月亮', value: 'fa-moon' },
      { label: '笑脸', value: 'fa-smile' },
      { label: '点赞', value: 'fa-thumbs-up' },
      { label: '相机', value: 'fa-camera' },
      { label: '调色板', value: 'fa-palette' },
      { label: '音乐', value: 'fa-music' },
      { label: '书籍', value: 'fa-book' },
      { label: '引用', value: 'fa-quote-left' },
      { label: '日历', value: 'fa-calendar-alt' },
      { label: '灵感', value: 'fa-lightbulb' },
      { label: '羽毛笔', value: 'fa-feather-alt' },
      { label: '钢笔', value: 'fa-pen-fancy' }
    ]

    const coverStyles = [
      { id: '11', name: '极简' },
      { id: '12', name: '现代' },
      { id: '13', name: '复古' },
      { id: '18', name: '太空' },
      { id: '19', name: '学术蓝' },
      { id: '20', name: '活力橙' },
      { id: '21', name: '清新绿' },
      { id: '22', name: '方格纸' },
      { id: '23', name: '考研金' },
      { id: '24', name: '高频话题' },
      { id: '25', name: '引用风' },
      { id: '26', name: '教育新' },
      { id: '27', name: '镜像反射' },
      { id: '28', name: '简约线' },
      { id: '29', name: '浅蓝左' },
      { id: '30', name: '浅黄竖' },
      { id: '31', name: '浅黄线' },
      { id: '32', name: '问号大' },
      { id: '33', name: '数字大' },
      { id: '34', name: '角标新' },
      { id: '35', name: '图标列' },
      { id: '36', name: '虚线框' },
      { id: '37', name: '左图标' },
      { id: '38', name: '底按钮' }
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
  max-width: 800px;
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

.cover-editor-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.cover-editor-title {
  font-size: 1.5em;
  margin-bottom: 15px;
  color: white;
  text-align: center;
  font-weight: 600;
}

.cover-editor-close {
  background: none;
  border: none;
  color: white;
  font-size: 24px;
  cursor: pointer;
  padding: 5px;
  transition: all 0.3s;
  opacity: 0.7;
}

.cover-editor-close:hover {
  opacity: 1;
  transform: scale(1.1);
}

.editor-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  max-height: calc(90vh - 150px);
  overflow-y: auto;
}

.editor-left {
  padding-right: 20px;
  border-right: 1px solid rgba(255, 255, 255, 0.1);
}

.editor-right {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding-top: 20px;
}

.cover-editor-section {
  width: 100%;
  margin-bottom: 20px;
  padding: 15px;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 10px;
}

.cover-editor-section-title {
  font-size: 1em;
  margin-bottom: 12px;
  color: rgba(255, 255, 255, 0.9);
  font-weight: 500;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  padding-bottom: 8px;
}

.cover-bg-type-selection {
  display: flex;
  gap: 15px;
  margin-bottom: 15px;
}

.cover-bg-option {
  background: rgba(255, 255, 255, 0.1);
  padding: 10px 15px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  transition: all 0.3s;
}

.cover-bg-option:hover {
  background: rgba(255, 255, 255, 0.2);
}

.cover-bg-option input[type="radio"] {
  margin: 0;
  cursor: pointer;
}

.cover-bg-option label {
  color: white;
  cursor: pointer;
  font-weight: 500;
}

.cover-input {
  width: 100%;
  padding: 10px 15px;
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  background: rgba(255, 255, 255, 0.1);
  color: white;
  font-size: 14px;
  margin-bottom: 12px;
  transition: all 0.3s;
}

.cover-input:focus {
  outline: none;
  border-color: var(--tech-blue);
  box-shadow: 0 0 10px rgba(99, 102, 241, 0.3);
}

.cover-input::placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.cover-font-controls-wrapper {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: 10px;
  width: 100%;
}

.cover-font-controls {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
  align-items: center;
  width: 100%;
}

.cover-font-family-select {
  grid-column: 1 / -1;
  min-width: 120px !important;
  max-width: none;
}

.cover-font-size-select,
.cover-font-weight-select,
.cover-text-align-select {
  min-width: 80px;
  max-width: none;
  width: 100%;
}

.color-picker-wrapper {
  display: flex;
  align-items: center;
  gap: 8px;
  justify-content: flex-end;
}

.cover-color-picker {
  width: 36px;
  height: 36px;
  padding: 3px;
  border-radius: 6px;
  background: rgba(255, 255, 255, 0.1);
  cursor: pointer;
  transition: all 0.3s;
}

.cover-color-picker:hover {
  background: rgba(255, 255, 255, 0.2);
}

.cover-color-picker::-webkit-color-swatch-wrapper {
  padding: 0;
}

.cover-color-picker::-webkit-color-swatch {
  border: none;
  border-radius: 4px;
}

.cover-preview {
  width: 280px;
  height: 373px;
  border-radius: 20px;
  overflow: hidden;
  position: relative;
  margin: 0 auto 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

.cover-card {
  width: 100%;
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
}

.cover-icon {
  font-size: 3em;
  margin-bottom: 20px;
  color: white;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
}

.cover-actions {
  display: flex;
  gap: 10px;
  margin-top: 20px;
  justify-content: center;
}

.cover-action-btn {
  padding: 10px 20px;
  border-radius: 30px;
  border: none;
  background: rgba(255, 255, 255, 0.9);
  color: #1e293b;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s;
  display: flex;
  align-items: center;
  gap: 5px;
}

.cover-action-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
  background: white;
}

.cover-action-btn.primary {
  background: var(--tech-blue);
  color: white;
}

.cover-action-btn.primary:hover {
  background: var(--tech-purple);
}

.cover-styles-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
  gap: 10px;
  margin-bottom: 20px;
  padding: 15px;
}

.cover-style-item {
  height: 80px;
  width: 80px !important;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s;
  position: relative;
  overflow: hidden;
  border: 2px solid transparent;
  margin: 0;
  padding: 0;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  box-sizing: border-box;
}

.cover-style-item span {
  color: #fff;
  font-size: 12px;
  position: absolute;
  bottom: 8px;
  width: 100%;
  text-align: center;
  font-family: 'Noto Sans SC', sans-serif;
  font-weight: 500;
  text-shadow: 0 1px 3px rgba(0,0,0,0.3);
  background: rgba(0,0,0,0.4);
  padding: 3px 0;
}

.cover-style-item:hover {
  transform: translateY(-3px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.cover-style-item.active {
  border-color: white;
  box-shadow: 0 0 15px rgba(255, 255, 255, 0.5);
}

.cover-date-controls,
.cover-author-controls {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 15px;
}

.cover-date-controls label,
.cover-author-controls label {
  color: white;
  font-weight: 500;
  margin-right: auto;
}

.cover-date-controls input[type="text"],
.cover-author-controls input[type="text"] {
  flex: 1;
  min-width: 0;
}

.cover-date-controls input[type="checkbox"],
.cover-author-controls input[type="checkbox"] {
  order: -1;
  margin: 0;
  cursor: pointer;
}

.cover-bg-controls {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.cover-bg-type {
  display: flex;
  gap: 15px;
}

.gradient-colors {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}

::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: rgba(255,255,255,0.1);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb {
  background: rgba(255,255,255,0.2);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: rgba(255,255,255,0.3);
}

/* 封面样式 */
/* 1. 极简主义风格 */
.cover-style-11 {
  background: #ffffff;
  position: relative;
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
  background: #0f0f0f;
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
  background: #f8f3e9;
  position: relative;
  overflow: hidden;
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
  background: #36454F;
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
  background: linear-gradient(135deg, #e6f7ff, #bae7ff, #91d5ff);
  position: relative;
  overflow: hidden;
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
  background: #fff9e6;
  position: relative;
  overflow: hidden;
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
  background: #f6ffed;
  position: relative;
  overflow: hidden;
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
  background-color: #fffbe6;
  background-image: linear-gradient(#fadb14 1px, transparent 1px),
                    linear-gradient(90deg, #fadb14 1px, transparent 1px);
  background-size: 20px 20px;
  background-position: -1px -1px;
  position: relative;
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
  background: #000000;
  position: relative;
  overflow: hidden;
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

/* 高频话题 - 考试主题 */
.cover-style-24 {
  background: #ffffff;
  position: relative;
  overflow: hidden;
  border: 2px solid #1890ff;
}

.cover-style-24::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 30px;
  background: #1890ff;
}

/* 引用风格 - 教育理念主题 */
.cover-style-25 {
  background: #f5f5f5;
  position: relative;
  overflow: hidden;
}

.cover-style-25::before {
  content: '\201C';
  position: absolute;
  top: 20px;
  left: 20px;
  font-size: 6em;
  font-family: Georgia, serif;
  color: #d9d9d9;
  line-height: 0.5;
}

.cover-style-25::after {
  content: '\201D';
  position: absolute;
  bottom: 0;
  right: 20px;
  font-size: 6em;
  font-family: Georgia, serif;
  color: #d9d9d9;
  line-height: 0.5;
}

/* 一键点亮 - 教育新视界主题 */
.cover-style-26 {
  background: #f0f5ff;
  position: relative;
  overflow: hidden;
}

.cover-style-26::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="30" height="30" viewBox="0 0 30 30"><circle cx="15" cy="15" r="3" fill="%232f54eb" fill-opacity="0.1"/></svg>');
}

/* 镜像反射 - 教育反思主题 */
.cover-style-27 {
  background: #ffffff;
  position: relative;
  overflow: hidden;
}

.cover-style-27::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, rgba(0,0,0,0.05) 25%, transparent 25%, transparent 50%, rgba(0,0,0,0.05) 50%, rgba(0,0,0,0.05) 75%, transparent 75%);
  background-size: 20px 20px;
  opacity: 0.5;
}

/* 简约线条 - 教育框架主题 */
.cover-style-28 {
  background: #fafafa;
  position: relative;
  overflow: hidden;
  border: 1px solid #f0f0f0;
}

.cover-style-28::before {
  content: '';
  position: absolute;
  top: 10px;
  left: 10px;
  right: 10px;
  bottom: 10px;
  border: 1px dashed #d9d9d9;
  pointer-events: none;
}

/* 浅蓝背景左对齐 */
.cover-style-29 {
  background: #f0f5ff;
  position: relative;
  overflow: hidden;
}

.cover-style-29::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #1890ff;
  opacity: 0.1;
  z-index: 0;
}

/* 浅黄背景居中双竖线 */
.cover-style-30 {
  background: #f5f0ff;
  position: relative;
  overflow: hidden;
}

.cover-style-30::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #9254de;
  opacity: 0.1;
  z-index: 0;
}

/* 浅黄背景居中黄线 */
.cover-style-31 {
  background: #e6fffb;
  position: relative;
  overflow: hidden;
}

.cover-style-31::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #13c2c2;
  opacity: 0.1;
  z-index: 0;
}

/* 浅黄背景问号放大 */
.cover-style-32 {
  background: #fff7e6;
  position: relative;
  overflow: hidden;
}

.cover-style-32::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #fa8c16;
  opacity: 0.1;
  z-index: 0;
}

/* 浅黄背景数字放大 */
.cover-style-33 {
  background: #f6ffed;
  position: relative;
  overflow: hidden;
}

.cover-style-33::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #52c41a;
  opacity: 0.1;
  z-index: 0;
}

/* 浅灰背景角标 */
.cover-style-34 {
  background: #fff1f0;
  position: relative;
  overflow: hidden;
}

.cover-style-34::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #f5222d;
  opacity: 0.1;
  z-index: 0;
}

/* 浅灰背景图标列表 */
.cover-style-35 {
  background: #fcffe6;
  position: relative;
  overflow: hidden;
}

.cover-style-35::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #a0d911;
  opacity: 0.1;
  z-index: 0;
}

/* 浅灰背景虚线框 */
.cover-style-36 {
  background: #e6f7ff;
  position: relative;
  overflow: hidden;
}

.cover-style-36::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #1890ff;
  opacity: 0.1;
  z-index: 0;
}

/* 浅灰背景左侧图标 */
.cover-style-37 {
  background: #f9f0ff;
  position: relative;
  overflow: hidden;
}

.cover-style-37::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #722ed1;
  opacity: 0.1;
  z-index: 0;
}

/* 浅灰背景底部按钮 */
.cover-style-38 {
  background: #fff2e8;
  position: relative;
  overflow: hidden;
}

.cover-style-38::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 8em;
  font-weight: 900;
  color: #fa541c;
  opacity: 0.1;
  z-index: 0;
}

.cover-style-38 .cover-button {
  display: block;
  text-align: center;
  padding: 8px 15px;
  border: 1px solid #fa541c;
  border-radius: 8px;
  margin: 15px auto 0;
  width: 80%;
  max-width: 200px;
  transition: background-color 0.3s ease;
  cursor: pointer;
  position: relative;
  z-index: 1;
  color: #d4380d;
  font-weight: 700;
}

.cover-style-38 .cover-button:hover {
  background-color: rgba(250, 84, 28, 0.1);
}

/* 响应式布局 */
@media (max-width: 768px) {
  .editor-content {
    grid-template-columns: 1fr;
  }

  .editor-left {
    padding-right: 0;
    border-right: none;
  }

  .cover-font-controls {
    grid-template-columns: 1fr;
  }
}
</style> 