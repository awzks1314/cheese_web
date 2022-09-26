<template>
  <el-dialog
    :title="!dataForm.vipTypeId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="类型名称" prop="vipTypeName">
      <el-input v-model="dataForm.vipTypeName" placeholder="类型名称"></el-input>
    </el-form-item>
    <el-form-item label="会员类型" prop="vipType">
      <el-input v-model="dataForm.vipType" placeholder="会员类型"></el-input>
    </el-form-item>
    <el-form-item label="价格(元)" prop="vipPrice">
      <el-input v-model="dataForm.vipPrice" placeholder="价格(元)"></el-input>
    </el-form-item>
    <el-form-item label="时长(天)" prop="vipLength">
      <el-input v-model="dataForm.vipLength" placeholder="时长(天)"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="sts">
      <el-input v-model="dataForm.sts" placeholder="状态"></el-input>
    </el-form-item>
    <el-form-item label="添加日期" prop="addDt">
      <el-input v-model="dataForm.addDt" placeholder="添加日期"></el-input>
    </el-form-item>
    <el-form-item label="添加时间" prop="addTm">
      <el-input v-model="dataForm.addTm" placeholder="添加时间"></el-input>
    </el-form-item>
    <el-form-item label="变更日期" prop="updateDt">
      <el-input v-model="dataForm.updateDt" placeholder="变更日期"></el-input>
    </el-form-item>
    <el-form-item label="变更时间" prop="updateTm">
      <el-input v-model="dataForm.updateTm" placeholder="变更时间"></el-input>
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
          vipTypeId: 0,
          vipTypeName: '',
          vipType: '',
          vipPrice: '',
          vipLength: '',
          sts: '',
          addDt: '',
          addTm: '',
          updateDt: '',
          updateTm: ''
        },
        dataRule: {
          vipTypeName: [
            { required: true, message: '类型名称不能为空', trigger: 'blur' }
          ],
          vipType: [
            { required: true, message: '会员类型不能为空', trigger: 'blur' }
          ],
          vipPrice: [
            { required: true, message: '价格不能为空', trigger: 'blur' }
          ],
          vipLength: [
            { required: true, message: '时长(天)不能为空', trigger: 'blur' }
          ],
          sts: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
          ],
          addDt: [
            { required: true, message: '添加日期不能为空', trigger: 'blur' }
          ],
          addTm: [
            { required: true, message: '添加时间不能为空', trigger: 'blur' }
          ],
          updateDt: [
            { required: true, message: '变更日期不能为空', trigger: 'blur' }
          ],
          updateTm: [
            { required: true, message: '变更时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.vipTypeId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.vipTypeId) {
            this.$http({
              url: this.$http.adornUrl(`/vip/viptype/info/${this.dataForm.vipTypeId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.vipTypeName = data.vipType.vipTypeName
                this.dataForm.vipType = data.vipType.vipType
                this.dataForm.vipPrice = data.vipType.vipPrice
                this.dataForm.vipLength = data.vipType.vipLength
                this.dataForm.sts = data.vipType.sts
                this.dataForm.addDt = data.vipType.addDt
                this.dataForm.addTm = data.vipType.addTm
                this.dataForm.updateDt = data.vipType.updateDt
                this.dataForm.updateTm = data.vipType.updateTm
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
              url: this.$http.adornUrl(`/vip/viptype/${!this.dataForm.vipTypeId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'vipTypeId': this.dataForm.vipTypeId || undefined,
                'vipTypeName': this.dataForm.vipTypeName,
                'vipType': this.dataForm.vipType,
                'vipPrice': this.dataForm.vipPrice,
                'vipLength': this.dataForm.vipLength,
                'sts': this.dataForm.sts,
                'addDt': this.dataForm.addDt,
                'addTm': this.dataForm.addTm,
                'updateDt': this.dataForm.updateDt,
                'updateTm': this.dataForm.updateTm
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
