<template>
  <el-dialog
    title="上传文件"
    :close-on-click-modal="false"
    @close="closeHandle"
    :visible.sync="visible">
    <el-form :model="dcumt" ref="dcumt">

      <el-form-item label="标题" prop="docmtType">
        <el-input v-model="dcumt.docmtTitle"></el-input>
      </el-form-item>
      <el-form-item label="文件类型" prop="docmtType">
        <el-radio-group v-model="dcumt.docmtType">
          <el-radio :label="1">书籍</el-radio>
          <el-radio :label="2">论文</el-radio>
          <el-radio :label="3">笔记</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="是否免费" prop="freeType">
        <el-radio-group v-model="dcumt.freeType">
          <el-radio :label="1">是</el-radio>
          <el-radio :label="2">否</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="价格" prop="docmtPrice">
        <el-input v-model="dcumt.docmtPrice"></el-input>
      </el-form-item>
      <el-form-item label="科目" prop="freeType">
        <el-select v-model="dcumt.docmtTag" clearable placeholder="请选择">
          <el-option
            v-for="item in docmtTags"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="拥有者" prop="docmtOwn">
        <el-input v-model="dcumt.docmtOwn"></el-input>
      </el-form-item>

      <el-form-item>
        <el-upload
          ref="myUp"
          :action="url"
          :data="dcumt"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          multiple
          :file-list="fileList"
          :auto-upload="false"
          :on-change="handleChange"
          style="text-align: center;">
          <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
          <el-button style="margin-left: 10px;" size="small" type="success"  @click="upload">上传到服务器</el-button>
        </el-upload>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        url: '',
        num: 0,
        successNum: 0,
        fileList: [],
        dcumt: {
          docmtType: '',
          docmtOwn: '',
          docmtPrice: '',
          freeType: '',
          docmtTitle: '',
          docmtTag: ''
        },
        docmtTags: [{
          value: '物理',
          label: '物理'
        }, {
          value: '化学',
          label: '化学'
        }, {
          value: '生物',
          label: '生物'
        }, {
          value: '英语',
          label: '英语'
        }, {
          value: '地理',
          label: '地理'
        }]
      }
    },
    methods: {
      init (id) {
        // this.docId.docId = id
        this.url = this.$http.adornUrl(`/document/upload?token=${this.$cookie.get('token')}`)
        this.visible = true
      },
      // 上传之前
      beforeUploadHandle (file) {
        // if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
        //   this.$message.error('只支持jpg、png、gif格式的图片！')
        //   return false
        // }
        this.num++
      },
      // 上传成功
      successHandle (response, file, fileList) {
        this.fileList = fileList
        this.successNum++
        if (response && response.code === 0) {
          if (this.num === this.successNum) {
            this.$confirm('操作成功, 是否继续操作?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).catch(() => {
              this.visible = false
            })
          }
        } else {
          this.$message.error(response.msg)
        }
      },
      // 弹窗关闭时
      closeHandle () {
        this.fileList = []
        this.$emit('refreshDataList')
      },

      // 新增
      // 通过onchanne触发方法获得文件列表
      handleChange (file, fileList) {
        this.fileList = fileList
        console.log(fileList)
      },
      upload () {
        this.$refs.myUp.submit()
      }
    }
  }
</script>
