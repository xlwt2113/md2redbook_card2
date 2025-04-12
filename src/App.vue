<script>
import { ref, reactive } from 'vue'
import { marked } from 'marked'
import CardList from './components/CardList.vue'
import CoverEditor from './components/CoverEditor.vue'

export default {
  name: 'App',
  components: {
    CardList,
    CoverEditor
  },
  setup() {
    const newsContent = ref('')
    const markdownContent = ref('')
    const currentStyle = ref('default')
    const currentTextAlign = ref('left')
    const currentFontSize = ref(16)
    const showCoverEditor = ref(false)
    const isConverting = ref(false)
    const cards = reactive([])

    const cardStyles = [
      { name: '默认', value: 'default' },
      { name: '赛博', value: 'cyberpunk' },
      { name: '霓虹', value: 'neon' },
      { name: '流光', value: 'glow' },
      { name: '渐变', value: 'gradient' },
      { name: '脉冲', value: 'dashed' },
      { name: '极光', value: 'aurora' },
      { name: '薄暮', value: 'twilight' },
      { name: '海洋', value: 'ocean' },
      { name: '森林', value: 'forest' },
      { name: '日落', value: 'sunset' },
      { name: '宇宙', value: 'cosmos' }
    ]

    const convertToMarkdown = async () => {
      isConverting.value = true
      try {
        const formData = new FormData()
        formData.append('news', newsContent.value)
        const response = await fetch('https://www.qianliuai.com/cgi-api/dify/tools/news_to_md', {
          method: 'POST',
          body: formData
        })
        const text = await response.text()
        markdownContent.value = text
        processInput()
        alert('转换成功')
      } catch (error) {
        console.error('Error:', error)
        alert('转换失败')
      } finally {
        isConverting.value = false
      }
    }

    const stripHtml = (html) => {
      const tmp = document.createElement('div')
      tmp.innerHTML = html
      return tmp.textContent || tmp.innerText || ''
    }

    const processInput = () => {
      // 清空现有卡片
      cards.length = 0
      
      // 如果输入为空，直接返回
      if (!markdownContent.value.trim()) return
      
      // 按空行分割内容块
      const contentBlocks = markdownContent.value
        .split(/\n\s*\n/)
        .filter(block => block.trim())
      
      // 处理每个内容块
      for (const block of contentBlocks) {
        const lines = block.split('\n')
        
        // 如果内容块为空，跳过
        if (lines.length === 0) continue
        
        // 第一行作为标题
        const titleHtml = marked.parse(lines[0])
        const titleText = stripHtml(titleHtml).trim() || '继续阅读'
        
        // 剩余行作为内容
        const contentHtml = lines.length > 1 
          ? marked.parse(lines.slice(1).join('\n'))
          : ''
        
        // 创建卡片
        cards.push({
          title: titleText,
          content: contentHtml,
          isCover: false
        })
      }
    }

    const adjustFontSize = (change) => {
      currentFontSize.value = Math.min(24, Math.max(12, currentFontSize.value + change))
      processInput()
    }

    const switchStyle = (style) => {
      currentStyle.value = style
    }

    const toggleTextAlign = () => {
      const alignments = ['left', 'center', 'right']
      const currentIndex = alignments.indexOf(currentTextAlign.value)
      currentTextAlign.value = alignments[(currentIndex + 1) % alignments.length]
      processInput()
    }

    const openCoverEditor = () => {
      showCoverEditor.value = true
    }

    const closeCoverEditor = () => {
      showCoverEditor.value = false
    }

    const saveCover = (coverData) => {
      cards.unshift(coverData)
      closeCoverEditor()
    }

    const deleteCard = (index) => {
      cards.splice(index, 1)
    }

    const downloadCard = async (card) => {
      try {
        const cardElement = document.querySelector(`.card:nth-child(${cards.indexOf(card) + 1})`)
        if (!cardElement) return
        
        // 临时隐藏下载和删除按钮
        const buttons = cardElement.querySelectorAll('.download-btn, .delete-btn')
        buttons.forEach(btn => btn.style.display = 'none')
        
        const canvas = await html2canvas(cardElement, {
          scale: 4,
          backgroundColor: null,
          useCORS: true
        })
        
        // 恢复按钮显示
        buttons.forEach(btn => btn.style.display = '')
        
        // 下载图片
        const link = document.createElement('a')
        link.download = `小红书卡片-${Date.now()}.png`
        link.href = canvas.toDataURL()
        link.click()
      } catch (error) {
        console.error('下载卡片失败:', error)
        alert('下载失败，请重试')
      }
    }

    const downloadAllCards = async () => {
      try {
        const zip = new JSZip()
        const imgFolder = zip.folder('小红书卡片')
        
        // 显示进度提示
        const progressDiv = document.createElement('div')
        progressDiv.style.cssText = `
          position: fixed;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
          background: rgba(0, 0, 0, 0.8);
          color: white;
          padding: 20px;
          border-radius: 10px;
          z-index: 9999;
          text-align: center;
        `
        progressDiv.innerHTML = `<div>正在生成卡片 <span id="progress">0/${cards.length}</span></div>`
        document.body.appendChild(progressDiv)
        
        for (let i = 0; i < cards.length; i++) {
          // 更新进度
          document.getElementById('progress').textContent = `${i+1}/${cards.length}`
          
          const cardElement = document.querySelector(`.card:nth-child(${i + 1})`)
          if (!cardElement) continue
          
          // 临时隐藏按钮
          const buttons = cardElement.querySelectorAll('.download-btn, .delete-btn')
          buttons.forEach(btn => btn.style.display = 'none')
          
          const canvas = await html2canvas(cardElement, {
            scale: 4,
            backgroundColor: null,
            useCORS: true
          })
          
          // 恢复按钮显示
          buttons.forEach(btn => btn.style.display = '')
          
          // 将canvas转换为blob并添加到zip
          const blob = await new Promise(resolve => canvas.toBlob(resolve))
          imgFolder.file(`小红书卡片-${i+1}.png`, blob)
          
          // 每处理3张卡片暂停一下，避免浏览器卡顿
          if ((i + 1) % 3 === 0) {
            await new Promise(resolve => setTimeout(resolve, 100))
          }
        }
        
        // 生成并下载zip文件
        progressDiv.innerHTML = `<div>正在打包下载...</div>`
        const content = await zip.generateAsync({ type: 'blob' })
        saveAs(content, `小红书卡片集合-${Date.now()}.zip`)
        
        // 移除进度提示
        setTimeout(() => {
          document.body.removeChild(progressDiv)
        }, 1000)
      } catch (error) {
        console.error('批量下载失败:', error)
        alert('批量下载失败，请重试')
      }
    }

    return {
      newsContent,
      markdownContent,
      currentStyle,
      currentTextAlign,
      currentFontSize,
      showCoverEditor,
      isConverting,
      cards,
      cardStyles,
      convertToMarkdown,
      processInput,
      adjustFontSize,
      switchStyle,
      toggleTextAlign,
      openCoverEditor,
      closeCoverEditor,
      saveCover,
      deleteCard,
      downloadCard,
      downloadAllCards
    }
  }
}
</script>

<template>
  <div class="app">
    <h1 class="tool-title">仟流Card</h1>
    <div class="main-container">
      <div class="editor-section">
        <div class="input-area">
          <textarea
            v-model="newsContent"
            class="news-input"
            placeholder="请输入原始文章内容"
          ></textarea>
          <button 
            :disabled="isConverting"
            @click="convertToMarkdown" 
            class="style-btn"
          >
            {{ isConverting ? '转化中......' : '转换成MarkDown' }}
          </button>
        </div>
        <div class="markdown-area">
          <div class="input-wrapper">
            <textarea
              v-model="markdownContent"
              class="markdown-editor"
              placeholder="输入Markdown或普通文本..."
              @input="processInput"
            ></textarea>
          </div>
          <div class="toolbar">
            <div class="toolbar-row">
              <div class="font-control">
                <button class="style-btn" @click="adjustFontSize(-2)">A-</button>
                <button class="style-btn" @click="adjustFontSize(2)">A+</button>
              </div>
              <button 
                v-for="style in cardStyles" 
                :key="style.name"
                class="style-btn"
                @click="switchStyle(style.value)"
              >
                {{ style.name }}
              </button>
              <button class="style-btn" @click="toggleTextAlign">
                <i :class="['fas', `fa-align-${currentTextAlign}`]"></i>
              </button>
              <button class="style-btn" @click="openCoverEditor">
                <i class="fas fa-image"></i> 创建封面
              </button>
              <button class="download-all-btn" @click="downloadAllCards">
                <i class="fas fa-download"></i> 下载全部卡片
              </button>
            </div>
          </div>
        </div>
      </div>
      <div class="card-container">
        <card-list
          :cards="cards"
          :currentStyle="currentStyle"
          :currentTextAlign="currentTextAlign"
          :currentFontSize="currentFontSize"
          @delete-card="deleteCard"
          @download-card="downloadCard"
        />
      </div>
    </div>
    <cover-editor
      v-if="showCoverEditor"
      :currentStyle="currentStyle"
      @close="closeCoverEditor"
      @save="saveCover"
    />
  </div>
</template>

<style>
/* 导入基础样式 */
@import './styles/base.css';
/* 导入卡片样式 */
@import './styles/card.css';
/* 导入工具栏样式 */
@import './styles/toolbar.css';

.app {
  min-height: 100vh;
  padding: 20px;
  background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
}

.main-container {
  display: flex;
  gap: 20px;
}

.editor-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.card-container {
  flex: 2;
}

/* 其他样式... */
</style>
