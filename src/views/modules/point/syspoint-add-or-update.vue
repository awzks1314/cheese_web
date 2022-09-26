<template>
  <el-dialog
    :title="!dataForm.pointId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="积分所属用户" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="积分所属用户"></el-input>
    </el-form-item>
    <el-form-item label="积分额度" prop="pointAmt">
      <el-input v-model="dataForm.pointAmt" placeholder="积分额度"></el-input>
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
          pointId: 0,
          userId: '',
          pointAmt: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '积分所属用户不能为空', trigger: 'blur' }
          ],
          pointAmt: [
            { required: true, message: '积分额度不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.pointId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.pointId) {
            this.$http({
              url: this.$http.adornUrl(`/point/info/${this.dataForm.pointId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.sysPoint.userId
                this.dataForm.pointAmt = data.sysPoint.pointAmt
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
              url: this.$http.adornUrl(`/point/${!this.dataForm.pointId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'pointId': this.dataForm.pointId || undefined,
                'userId': this.dataForm.userId,
                'pointAmt': this.dataForm.pointAmt
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
