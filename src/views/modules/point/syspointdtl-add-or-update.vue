<template>
  <el-dialog
    :title="!dataForm.pointDtlId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="明细额度" prop="pointDelAmt">
      <el-input v-model="dataForm.pointDelAmt" placeholder="明细额度"></el-input>
    </el-form-item>
    <el-form-item label="类型" prop="pointType">
      <el-input v-model="dataForm.pointType" placeholder="类型"></el-input>
    </el-form-item>
    <el-form-item label="积分" prop="pointDirection">
      <el-input v-model="dataForm.pointDirection" placeholder="积分"></el-input>
    </el-form-item>
    <el-form-item label="积分日期" prop="createDt">
      <el-input v-model="dataForm.createDt" placeholder="积分日期"></el-input>
    </el-form-item>
    <el-form-item label="积分时间" prop="createTm">
      <el-input v-model="dataForm.createTm" placeholder="积分时间"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="pointMemo">
      <el-input v-model="dataForm.pointMemo" placeholder="备注"></el-input>
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
          pointDtlId: 0,
          userId: '',
          pointDelAmt: '',
          pointType: '',
          pointDirection: '',
          createDt: '',
          createTm: '',
          pointMemo: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          pointDelAmt: [
            { required: true, message: '积分明细额度不能为空', trigger: 'blur' }
          ],
          pointType: [
            { required: true, message: '积分类型 1-签到收入积分 2-分享收入积分 3-下载支出积分不能为空', trigger: 'blur' }
          ],
          pointDirection: [
            { required: true, message: '积分方向 0-收入 2支出不能为空', trigger: 'blur' }
          ],
          createDt: [
            { required: true, message: '积分发生日期不能为空', trigger: 'blur' }
          ],
          createTm: [
            { required: true, message: '积分发生时间不能为空', trigger: 'blur' }
          ],
          pointMemo: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.pointDtlId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.pointDtlId) {
            this.$http({
              url: this.$http.adornUrl(`/point/info/${this.dataForm.pointDtlId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.sysPointDtl.userId
                this.dataForm.pointDelAmt = data.sysPointDtl.pointDelAmt
                this.dataForm.pointType = data.sysPointDtl.pointType
                this.dataForm.pointDirection = data.sysPointDtl.pointDirection
                this.dataForm.createDt = data.sysPointDtl.createDt
                this.dataForm.createTm = data.sysPointDtl.createTm
                this.dataForm.pointMemo = data.sysPointDtl.pointMemo
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
              url: this.$http.adornUrl(`/point/${!this.dataForm.pointDtlId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'pointDtlId': this.dataForm.pointDtlId || undefined,
                'userId': this.dataForm.userId,
                'pointDelAmt': this.dataForm.pointDelAmt,
                'pointType': this.dataForm.pointType,
                'pointDirection': this.dataForm.pointDirection,
                'createDt': this.dataForm.createDt,
                'createTm': this.dataForm.createTm,
                'pointMemo': this.dataForm.pointMemo
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
