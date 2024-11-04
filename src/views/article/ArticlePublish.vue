<template>
  <div class="page-content">
    <div class="editor-wrap">
      <!-- 文章标题、类型 -->
      <el-row :gutter="4">
        <el-col :span="20">
          <el-input v-model.trim="articleData.title" placeholder="文章标题" maxlength="100" />
        </el-col>
        <el-col :span="4">
          <el-button type="primary" plain round style="margin-left: 16px">保存草稿</el-button>
          <el-button type="primary" round style="margin-left: 8px">发布文章</el-button>
          <el-button icon="Setting" style="margin-left: 8px" circle />
        </el-col>
      </el-row>

      <!-- 富文本编辑器 -->
      <!--          <editor class="el-top" v-model="editorHtml"></editor>-->
      <MdEditor ref="editorRef" class="el-top" v-model="articleData.content" :previewTheme="editorProp.previewTheme"
                :codeTheme="editorProp.codeTheme"
                noUploadImg
                :toolbars="editorProp.toolbars">
        <template #defToolbars>
          <ModalToolbar
            :visible="editorProp.resource.visible"
            :isFullscreen="editorProp.resource.modalFullscreen"
            showAdjust
            title="素材库"
            modalTitle="素材库"
            width="870px"
            height="600px"
            @onClick="editorProp.resource.visible = true"
            @onClose="editorProp.resource.visible = false"
            @onAdjust="editorProp.resource.modalFullscreen = !editorProp.resource.modalFullscreen"
          >
            <button @click="handler">上传图片</button>
            <template #trigger>
              <el-icon>
                <Picture />
              </el-icon>
            </template>
          </ModalToolbar>
          <DropdownToolbar title="预览主题" :visible="editorProp.previewThemeSwitch.visible"
                           :onChange="editorProp.previewThemeSwitch.onVisibleChange">
            <template #overlay>
              <div class="theme-switch">
                <ol>
                  <li
                    class="theme-item"
                    v-for="(theme, index) of editorProp.previewThemeSwitch.list"
                    :key="index"
                    @click="editorProp.previewThemeSwitch.themeChange(theme)"
                    v-text="theme"
                  ></li>
                </ol>
              </div>
            </template>
            <template #trigger>
              <el-icon>
                <DataAnalysis />
              </el-icon>
            </template>
          </DropdownToolbar>
          <DropdownToolbar title="代码主题" :visible="editorProp.codeThemeSwitch.visible"
                           :onChange="editorProp.codeThemeSwitch.onVisibleChange">
            <template #overlay>
              <div class="theme-switch">
                <ol>
                  <li class="theme-item"
                      v-for="(theme, index) of editorProp.codeThemeSwitch.list"
                      :key="index"
                      @click="editorProp.codeThemeSwitch.themeChange(theme)"
                      v-text="theme"
                  ></li>
                </ol>
              </div>
            </template>
            <template #trigger>
              <el-icon>
                <Monitor />
              </el-icon>
            </template>
          </DropdownToolbar>
        </template>
      </MdEditor>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { PageModeEnum } from '@/enums/formEnum'
  import axios from 'axios'

  import { MdEditor, ModalToolbar, DropdownToolbar } from 'md-editor-v3'
  import 'md-editor-v3/lib/style.css'

  const route = useRoute()
  const router = useRouter()
  const editorRef = ref(null)

  const articleData = ref({
    title: '',
    content: '',
    summary: ''
  })

  const editorProp = ref({
    toolbars: [
      'bold',
      'underline',
      'italic',
      '-',
      'title',
      'strikeThrough',
      'sub',
      'sup',
      'quote',
      'unorderedList',
      'orderedList',
      'task',
      '-',
      'codeRow',
      'code',
      'link',
      0,
      'table',
      'mermaid',
      'katex',
      '-',
      1,
      2,
      '=',
      'pageFullscreen',
      'fullscreen',
      'preview',
      'previewOnly',
      'catalog'
    ],
    previewTheme: 'smart-blue',
    codeTheme: 'vuepress',
    resource: {
      visible: false,
      modalFullscreen: false
    },
    previewThemeSwitch: {
      visible: false,
      list: ['default', 'github', 'vuepress', 'mk-cute', 'smart-blue', 'cyanosis'],
      onVisibleChange: () => {
        editorProp.value.previewThemeSwitch.visible = !editorProp.value.previewThemeSwitch.visible
      },
      themeChange: (theme: string) => {
        editorProp.value.previewTheme = theme
      }
    },
    codeThemeSwitch: {
      visible: false,
      list: ['atom', 'a11y', 'github', 'gradient', 'kimbie', 'paraiso', 'qtcreator', 'stackoverflow'],
      onVisibleChange: () => {
        editorProp.value.codeThemeSwitch.visible = !editorProp.value.codeThemeSwitch.visible
      },
      themeChange: (theme: string) => {
        editorProp.value.codeTheme = theme
      }
    }
  })

  let pageMode: PageModeEnum = PageModeEnum.Add // 页面类型 新增 ｜ 编辑

  onMounted(() => {
    scrollToTop()
    initPageMode()
  })

  const handler = () => {
    alert('自定义按钮')
    // 此处添加素材库图片
    editorRef.value?.execCommand('bold')
  }

  // 初始化页面类型 新增 ｜ 编辑
  const initPageMode = () => {
    const { id } = route.query
    pageMode = id ? PageModeEnum.Edit : PageModeEnum.Add
    if (pageMode === PageModeEnum.Edit && id) {
      // 编辑
    } else {
      // 新增
    }
  }

  // 提交
  const submit = () => {
    if (pageMode === PageModeEnum.Edit) {
      editArticle()
    } else {
      addArticle()
    }
  }

  // 添加文章
  const addArticle = async () => {
    // 新增文章
  }

  // 编辑文章
  const editArticle = async () => {
    // 编辑文章
  }

  // 获取摘要
  const getSummary = (maxlength: number = 200) => {
    articleData.value.summary = articleData.value.content.replace(/<[^>]+>/g, '').substring(0, maxlength)
  }

  // 返回上一页
  const goBack = () => {
    setTimeout(() => {
      router.go(-1)
    }, 800)
  }

  const scrollToTop = () => {
    window.scrollTo({ top: 0, behavior: 'smooth' })
  }

</script>

<style lang="scss" scoped>
  .page-content {
    height: 100%;

    .editor-wrap {
      width: 100%;

      .el-top {
        margin-top: 12px;
      }

      .md-editor {
        height: 82vh;

        .theme-switch {
          background-color: var(--md-bk-color);
          border-radius: 3px;
          border: 1px solid var(--md-border-color);

          .theme-item {
            font-size: 14px;
            padding: 4px 10px;
            border-radius: 5px;

            &:hover {
              background-color: var(--md-bk-hover-color);
              cursor: pointer;
            }
          }
        }
      }
    }
  }
</style>
