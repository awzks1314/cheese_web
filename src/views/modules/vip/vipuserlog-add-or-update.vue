<template>
  <el-dialog
    :title="!dataForm.logId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="VIP套餐ID" prop="vipTypeId">
      <el-input v-model="dataForm.vipTypeId" placeholder="VIP套餐ID"></el-input>
    </el-form-item>
    <el-form-item label="VIP购买日期" prop="vipPayDt">
      <el-input v-model="dataForm.vipPayDt" placeholder="VIP购买日期"></el-input>
    </el-form-item>
    <el-form-item label="VIP购买时间" prop="vipPayTm">
      <el-input v-model="dataForm.vipPayTm" placeholder="VIP购买时间"></el-input>
    </el-form-item>
    <el-form-item label="生效日期" prop="vipEffectDt">
      <el-input v-model="dataForm.vipEffectDt" placeholder="生效日期"></el-input>
    </el-form-item>
    <el-form-item label="生效时间" prop="vipEffectTm">
      <el-input v-model="dataForm.vipEffectTm" placeholder="生效时间"></el-input>
    </el-form-item>
    <el-form-item label="失效日期" prop="vipLoseDt">
      <el-input v-model="dataForm.vipLoseDt" placeholder="失效日期"></el-input>
    </el-form-item>
    <el-form-item label="失效时间" prop="vipLoseTm">
      <el-input v-model="dataForm.vipLoseTm" placeholder="失效时间"></el-input>
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
          vipTypeId: '',
          vipPayDt: '',
          vipPayTm: '',
          vipEffectDt: '',
          vipEffectTm: '',
          vipLoseDt: '',
          vipLoseTm: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          vipTypeId: [
            { required: true, message: 'VIP套餐ID不能为空', trigger: 'blur' }
          ],
          vipPayDt: [
            { required: true, message: 'VIP购买支付日期不能为空', trigger: 'blur' }
          ],
          vipPayTm: [
            { required: true, message: 'VIP购买支付时间不能为空', trigger: 'blur' }
          ],
          vipEffectDt: [
            { required: true, message: '生效日期不能为空', trigger: 'blur' }
          ],
          vipEffectTm: [
            { required: true, message: '生效时间不能为空', trigger: 'blur' }
          ],
          vipLoseDt: [
            { required: true, message: '失效日期不能为空', trigger: 'blur' }
          ],
          vipLoseTm: [
            { required: true, message: '失效时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/vip/vipuserlog/info/${this.dataForm.logId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.vipUserLog.userId
                this.dataForm.vipTypeId = data.vipUserLog.vipTypeId
                this.dataForm.vipPayDt = data.vipUserLog.vipPayDt
                this.dataForm.vipPayTm = data.vipUserLog.vipPayTm
                this.dataForm.vipEffectDt = data.vipUserLog.vipEffectDt
                this.dataForm.vipEffectTm = data.vipUserLog.vipEffectTm
                this.dataForm.vipLoseDt = data.vipUserLog.vipLoseDt
                this.dataForm.vipLoseTm = data.vipUserLog.vipLoseTm
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
              url: this.$http.adornUrl(`/vip/vipuserlog/${!this.dataForm.logId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'logId': this.dataForm.logId || undefined,
                'userId': this.dataForm.userId,
                'vipTypeId': this.dataForm.vipTypeId,
                'vipPayDt': this.dataForm.vipPayDt,
                'vipPayTm': this.dataForm.vipPayTm,
                'vipEffectDt': this.dataForm.vipEffectDt,
                'vipEffectTm': this.dataForm.vipEffectTm,
                'vipLoseDt': this.dataForm.vipLoseDt,
                'vipLoseTm': this.dataForm.vipLoseTm
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
