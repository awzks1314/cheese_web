<template>
  <el-dialog
    :title="!dataForm.fvrtId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="收藏类型 " prop="fvrtType">
      <el-input v-model="dataForm.fvrtType" placeholder="收藏类型 "></el-input>
    </el-form-item>
    <el-form-item label="收藏的内容的ID" prop="fvrtContentId">
      <el-input v-model="dataForm.fvrtContentId" placeholder="收藏的内容的ID"></el-input>
    </el-form-item>
    <el-form-item label="收藏日期" prop="createDt">
      <el-input v-model="dataForm.createDt" placeholder="收藏日期"></el-input>
    </el-form-item>
    <el-form-item label="收藏时间" prop="createTm">
      <el-input v-model="dataForm.createTm" placeholder="收藏时间"></el-input>
    </el-form-item>
    <el-form-item label="收藏状态" prop="fvrtSts">
      <el-input v-model="dataForm.fvrtSts" placeholder="收藏状态"></el-input>
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
          fvrtId: 0,
          userId: '',
          fvrtType: '',
          fvrtContentId: '',
          createDt: '',
          createTm: '',
          fvrtSts: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          fvrtType: [
            { required: true, message: '收藏类型 不能为空', trigger: 'blur' }
          ],
          fvrtContentId: [
            { required: true, message: '收藏的内容的ID不能为空', trigger: 'blur' }
          ],
          createDt: [
            { required: true, message: '收藏日期不能为空', trigger: 'blur' }
          ],
          createTm: [
            { required: true, message: '收藏时间不能为空', trigger: 'blur' }
          ],
          fvrtSts: [
            { required: true, message: '收藏状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.fvrtId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.fvrtId) {
            this.$http({
              url: this.$http.adornUrl(`/favorite/info/${this.dataForm.fvrtId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.userFavorite.userId
                this.dataForm.fvrtType = data.userFavorite.fvrtType
                this.dataForm.fvrtContentId = data.userFavorite.fvrtContentId
                this.dataForm.createDt = data.userFavorite.createDt
                this.dataForm.createTm = data.userFavorite.createTm
                this.dataForm.fvrtSts = data.userFavorite.fvrtSts
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
              url: this.$http.adornUrl(`/favorite/${!this.dataForm.fvrtId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'fvrtId': this.dataForm.fvrtId || undefined,
                'userId': this.dataForm.userId,
                'fvrtType': this.dataForm.fvrtType,
                'fvrtContentId': this.dataForm.fvrtContentId,
                'createDt': this.dataForm.createDt,
                'createTm': this.dataForm.createTm,
                'fvrtSts': this.dataForm.fvrtSts
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
