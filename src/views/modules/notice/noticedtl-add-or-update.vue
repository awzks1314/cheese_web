<template>
  <el-dialog
    :title="!dataForm.noticeDtlId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="通知ID" prop="noticeId">
      <el-input v-model="dataForm.noticeId" placeholder="通知ID"></el-input>
    </el-form-item>
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="sts">
      <el-input v-model="dataForm.sts" placeholder="状态"></el-input>
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
          noticeDtlId: 0,
          noticeId: '',
          userId: '',
          sts: ''
        },
        dataRule: {
          noticeId: [
            { required: true, message: '通知ID不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          sts: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.noticeDtlId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.noticeDtlId) {
            this.$http({
              url: this.$http.adornUrl(`/notice/noticedtl/info/${this.dataForm.noticeDtlId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.noticeId = data.noticeDtl.noticeId
                this.dataForm.userId = data.noticeDtl.userId
                this.dataForm.sts = data.noticeDtl.sts
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
              url: this.$http.adornUrl(`/notice/noticedtl/${!this.dataForm.noticeDtlId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'noticeDtlId': this.dataForm.noticeDtlId || undefined,
                'noticeId': this.dataForm.noticeId,
                'userId': this.dataForm.userId,
                'sts': this.dataForm.sts
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
