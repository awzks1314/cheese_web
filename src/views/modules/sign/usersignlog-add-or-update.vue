<template>
  <el-dialog
    :title="!dataForm.logId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="userId">
      <el-input v-model="dataForm.userId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="signDt">
      <el-input v-model="dataForm.signDt" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="signTm">
      <el-input v-model="dataForm.signTm" placeholder=""></el-input>
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
          logId: 0,
          userId: '',
          signDt: '',
          signTm: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          signDt: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          signTm: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.logId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.logId) {
            this.$http({
              url: this.$http.adornUrl(`/sign/usersignlog/info/${this.dataForm.logId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.userSignLog.userId
                this.dataForm.signDt = data.userSignLog.signDt
                this.dataForm.signTm = data.userSignLog.signTm
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
              url: this.$http.adornUrl(`/sign/usersignlog/${!this.dataForm.logId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'logId': this.dataForm.logId || undefined,
                'userId': this.dataForm.userId,
                'signDt': this.dataForm.signDt,
                'signTm': this.dataForm.signTm
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
