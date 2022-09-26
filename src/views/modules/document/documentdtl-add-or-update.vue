<template>
  <el-dialog
    :title="!dataForm.docTdlId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="docId">
      <el-input v-model="dataForm.docId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="docContent">
      <el-input v-model="dataForm.docContent" placeholder=""></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          docTdlId: 0,
          docId: '',
          docContent: ''
        },
        dataRule: {
          docId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          docContent: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.docTdlId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.docTdlId) {
            this.$http({
              url: this.$http.adornUrl(`/document/documentdtl/info/${this.dataForm.docTdlId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.docId = data.documentDtl.docId
                this.dataForm.docContent = data.documentDtl.docContent
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/document/documentdtl/${!this.dataForm.docTdlId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'docTdlId': this.dataForm.docTdlId || undefined,
                'docId': this.dataForm.docId,
                'docContent': this.dataForm.docContent
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
