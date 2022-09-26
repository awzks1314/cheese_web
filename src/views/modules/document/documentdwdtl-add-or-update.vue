<template>
  <el-dialog
    :title="!dataForm.dwDtlId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="文档ID" prop="docmId">
      <el-input v-model="dataForm.docmId" placeholder="文档ID"></el-input>
    </el-form-item>
    <el-form-item label="下载者" prop="dwUser">
      <el-input v-model="dataForm.dwUser" placeholder="下载者"></el-input>
    </el-form-item>
    <el-form-item label="下载日期" prop="dwDt">
      <el-input v-model="dataForm.dwDt" placeholder="下载日期"></el-input>
    </el-form-item>
    <el-form-item label="下载时间" prop="dwTm">
      <el-input v-model="dataForm.dwTm" placeholder="下载时间"></el-input>
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
          dwDtlId: 0,
          docmId: '',
          dwUser: '',
          dwDt: '',
          dwTm: ''
        },
        dataRule: {
          docmId: [
            { required: true, message: '文档ID不能为空', trigger: 'blur' }
          ],
          dwUser: [
            { required: true, message: '下载者不能为空', trigger: 'blur' }
          ],
          dwDt: [
            { required: true, message: '下载日期不能为空', trigger: 'blur' }
          ],
          dwTm: [
            { required: true, message: '下载时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.dwDtlId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.dwDtlId) {
            this.$http({
              url: this.$http.adornUrl(`/document/documentdwdtl/info/${this.dataForm.dwDtlId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.docmId = data.documentDwDtl.docmId
                this.dataForm.dwUser = data.documentDwDtl.dwUser
                this.dataForm.dwDt = data.documentDwDtl.dwDt
                this.dataForm.dwTm = data.documentDwDtl.dwTm
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
              url: this.$http.adornUrl(`/document/documentdwdtl/${!this.dataForm.dwDtlId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dwDtlId': this.dataForm.dwDtlId || undefined,
                'docmId': this.dataForm.docmId,
                'dwUser': this.dataForm.dwUser,
                'dwDt': this.dataForm.dwDt,
                'dwTm': this.dataForm.dwTm
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
