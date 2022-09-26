<template>
  <el-dialog
    :title="!dataForm.likeDtlId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="话题ID" prop="topicId">
      <el-input v-model="dataForm.topicId" placeholder="话题ID"></el-input>
    </el-form-item>
    <el-form-item label="点赞者ID" prop="likeUserId">
      <el-input v-model="dataForm.likeUserId" placeholder="点赞者ID"></el-input>
    </el-form-item>
    <el-form-item label="点赞日期" prop="createDt">
      <el-input v-model="dataForm.createDt" placeholder="点赞日期"></el-input>
    </el-form-item>
    <el-form-item label="点赞时间" prop="createTm">
      <el-input v-model="dataForm.createTm" placeholder="点赞时间"></el-input>
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
          likeDtlId: 0,
          topicId: '',
          likeUserId: '',
          createDt: '',
          createTm: ''
        },
        dataRule: {
          topicId: [
            { required: true, message: '话题ID不能为空', trigger: 'blur' }
          ],
          likeUserId: [
            { required: true, message: '点赞者ID不能为空', trigger: 'blur' }
          ],
          createDt: [
            { required: true, message: '点赞日期不能为空', trigger: 'blur' }
          ],
          createTm: [
            { required: true, message: '点赞时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.likeDtlId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.likeDtlId) {
            this.$http({
              url: this.$http.adornUrl(`/topic/topiclikedtl/info/${this.dataForm.likeDtlId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.topicId = data.topicLikeDtl.topicId
                this.dataForm.likeUserId = data.topicLikeDtl.likeUserId
                this.dataForm.createDt = data.topicLikeDtl.createDt
                this.dataForm.createTm = data.topicLikeDtl.createTm
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
              url: this.$http.adornUrl(`/topic/topiclikedtl/${!this.dataForm.likeDtlId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'likeDtlId': this.dataForm.likeDtlId || undefined,
                'topicId': this.dataForm.topicId,
                'likeUserId': this.dataForm.likeUserId,
                'createDt': this.dataForm.createDt,
                'createTm': this.dataForm.createTm
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
