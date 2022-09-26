<template>
  <el-dialog
    :title="!dataForm.cmmtLike ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="评论ID" prop="cmmtId">
      <el-input v-model="dataForm.cmmtId" placeholder="评论ID"></el-input>
    </el-form-item>
    <el-form-item label="点赞者" prop="cmmtLikeUser">
      <el-input v-model="dataForm.cmmtLikeUser" placeholder="点赞者"></el-input>
    </el-form-item>
    <el-form-item label="点赞日期" prop="cmmtLikeDt">
      <el-input v-model="dataForm.cmmtLikeDt" placeholder="点赞日期"></el-input>
    </el-form-item>
    <el-form-item label="点赞时间" prop="cmmtLikeTm">
      <el-input v-model="dataForm.cmmtLikeTm" placeholder="点赞时间"></el-input>
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
          cmmtLike: 0,
          cmmtId: '',
          cmmtLikeUser: '',
          cmmtLikeDt: '',
          cmmtLikeTm: ''
        },
        dataRule: {
          cmmtId: [
            { required: true, message: '评论ID不能为空', trigger: 'blur' }
          ],
          cmmtLikeUser: [
            { required: true, message: '评论点赞者不能为空', trigger: 'blur' }
          ],
          cmmtLikeDt: [
            { required: true, message: '评论点赞日期不能为空', trigger: 'blur' }
          ],
          cmmtLikeTm: [
            { required: true, message: '评论点赞时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.cmmtLike = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.cmmtLike) {
            this.$http({
              url: this.$http.adornUrl(`/topic/topiccmmtlike/info/${this.dataForm.cmmtLike}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.cmmtId = data.topicCmmtLike.cmmtId
                this.dataForm.cmmtLikeUser = data.topicCmmtLike.cmmtLikeUser
                this.dataForm.cmmtLikeDt = data.topicCmmtLike.cmmtLikeDt
                this.dataForm.cmmtLikeTm = data.topicCmmtLike.cmmtLikeTm
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
              url: this.$http.adornUrl(`/topic/topiccmmtlike/${!this.dataForm.cmmtLike ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'cmmtLike': this.dataForm.cmmtLike || undefined,
                'cmmtId': this.dataForm.cmmtId,
                'cmmtLikeUser': this.dataForm.cmmtLikeUser,
                'cmmtLikeDt': this.dataForm.cmmtLikeDt,
                'cmmtLikeTm': this.dataForm.cmmtLikeTm
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
