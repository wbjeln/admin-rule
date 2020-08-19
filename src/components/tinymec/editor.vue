<template>
  <div class="tinymce-editor">
    <editor v-model="myValue" :init="init" :disabled="disabled" @onClick="onClick" />
  </div>
</template>

<script>
import request from '@/utils/request'
import tinymce from 'tinymce/tinymce'
import Editor from '@tinymce/tinymce-vue'
import 'tinymce/themes/mobile'
import 'tinymce/themes/silver'
import 'tinymce/icons/default/icons'
// 编辑器插件plugins
// 更多插件参考：https://www.tiny.cloud/docs/plugins/
import 'tinymce/plugins/image'// 插入上传图片插件
import 'tinymce/plugins/media'// 插入视频插件
import 'tinymce/plugins/table'// 插入表格插件
import 'tinymce/plugins/lists'// 列表插件
import 'tinymce/plugins/wordcount'// 字数统计插件
import 'tinymce/plugins/print'// 打印
import 'tinymce/plugins/preview'// 预览
import 'tinymce/plugins/code'// code
import 'tinymce/plugins/link'// 插入链接
import 'tinymce/plugins/searchreplace'// 查找插件
export default {
  components: {
    Editor
  },
  props: {
    value: {
      type: String,
      default: ''
    },
    disabled: {
      type: Boolean,
      default: false
    },
    plugins: {
      type: [String, Array],
      default: 'lists image media table wordcount print preview code link searchreplace'
    },
    toolbar: {
      type: [String, Array],
      default: 'undo redo | fontsizeselect formatselect | bold italic forecolor backcolor | alignleft aligncenter alignright alignjustify | bullist numlist outdent indent | lists image media link table | searchreplace removeformat code preview print'
    }
  },
  data() {
    return {
      init: {
        language_url: '/tinymce/langs/zh_CN.js',
        language: 'zh_CN',
        skin_url: '/tinymce/skins/ui/oxide',
        // skin_url: '/tinymce/skins/ui/oxide-dark', // 暗色系
        height: 300,
        plugins: this.plugins,
        toolbar: this.toolbar,
        fontsize_formats: '12px 14px 16px 18px 20px 24px 36px 48px 64px 96px',
        branding: false,
        image_description: false, // 图片描述
        link_title: false, // 链接标题
        menubar: false,
        // 此处为图片上传处理函数，这个直接用了base64的图片形式上传图片，
        // 如需ajax上传可参考https://www.tiny.cloud/docs/configure/file-image-upload/#images_upload_handler
        images_upload_handler: (blobInfo, success, failure) => {
          var file = blobInfo.blob()
          console.log(file)
          var fd = new FormData()// eslint-disable-line no-unused-vars
          fd.append('myfile[]', file)
          request({
            method: 'post',
            url: `/uploadSignalImg?token=5dac2df9e3278d729f40d838313628c3`,
            data: fd
          })
            .then(res => {
              console.log(res)
              const img = 'http://ldcd.netfishing.cn' + res.message
              success(img)
            })
          // const img = 'data:image/jpeg;base64,' + blobInfo.base64()
          // success(img)
        }
      },
      myValue: this.value
    }
  },
  watch: {
    value(newValue) {
      this.myValue = newValue
    },
    myValue(newValue) {
      this.$emit('input', newValue)
    }
  },
  mounted() {
    tinymce.init({})
  },
  methods: {
    // 添加相关的事件，可用的事件参照文档=> https://github.com/tinymce/tinymce-vue => All available events
    // 需要什么事件可以自己增加
    onClick(e) {
      this.$emit('onClick', e, tinymce)
    },
    // 可以添加一些自己的自定义事件，如清空内容
    clear() {
      this.myValue = ''
    }
  }
}

</script>
